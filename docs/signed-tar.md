---
title: signed tar
subtitle: Verify file packages with minimal overhead.
layout: page
---


This document describes a method for producing cryptographically
signed [tar](https://en.wikipedia.org/wiki/Tar_%28computing%29) files.
The suggested file extension is `stf`.

## Basic setup

Any files can be placed inside a tar archive, excluding
symlinks and other shenanigans. Just plain files.

There are two reserved filenames inside the tar:

* `_manifest`
* `_manifest.sig`

## Tar manifest: `_manifest`

The `_manifest` is a plain-text ASCII file, listing the *SHA-256*
hashes of the files and the [hash](./cryptography) of the
public key used to sign the manifest.

An example `_manifest` file might look like:

	[manifest]
	key=751306bfafae00f2417a7b7c7b7750e263838dc1
	84784533571ed086a3cdff9fd41f89b43863a7314660442616fd02ee51a9608b=file.ext
	3de4c18609d8069edf84538a0b4d27140565b72f894c35701a3a737353cb5fe3=folder path/file.ext

The file starts with the header `[manifest]`.

The property `key` is the *hash* of the public key used to sign
the manifest.

Following this is a list of *all* files inside the tar archive,
except the two manifest files. The "property" is the *SHA-256* hash
of the *file*, and the "value" is the file name.

File naming restrictions:

* The filename uses all characters after the `=` up to the end of the line.
* The file name is constructed using the normal *unix* syntax of a forward
	slash (`/`) as the folder separator.

The file structure inside the tar archive for the above manifest would
look something like this:

	package.tar/
	  _manifest
	  _manifest.sig
	  file.ext
	  folder path/
	    file.ext

## Manifest signature: `_manifest.sig`

The `_manifest.sig` is a binary signature file. The signature is generated
by taking the *SHA-256* of the `_manifest` file and signing the *hash*
with the [public key](./cryptography).

In the above example `_manifest` file, the *SHA-256* hash would be:

	3656ad41d1c95306d324c827faf88bce354a4f27c0ba220c60cd10baa4ec8e99

This hash is signed, and the signature and stored in binary format as
the `_manifest.sig` file.

## A complete example

The files used in this example are found in the
[example folder](https://github.com/sdmp/signed-tar/). The
files named `file.ext` are simply random bytes
generated using [random.org](http://random.org).

First start by generating an OpenPGP key. In this example we use [GPG](https://www.gnupg.org/).

Create a new key:

	gpg --gen-key

Select the option `RSA and RSA`, and then select a bit size
of **at least** `2048 bits`.

In this example the expiration date does not matter, so we
will select `0 = key does not expire`.

For this example we use the name:

	John Doe (Example user) <john.doe@email.com>

And the password:

	abc.123.my.super.secret.password

In order for you to follow this demo and verify the outputs, the keys
generated for this example were exported to an
[example folder](https://github.com/sdmp/signed-tar/) using the
following commands:

Public key:

	gpg --armor --export john.doe@email.com

Private key:

	gpg --armor --output demo_key.asc --export-secret-keys John

Using GPG and generating a "detached" signature is equivalent
to generating the file hash and signing it. Therefore, signing
the `_manifest` file is done:

	gpg --detach-sign _manifest

GPG automatically generates the signature file named `_manifest.sig`.

Together these files are placed inside the folder such that
the folder looks like:

	package/
	  _manifest
	  _manifest.sig
	  file.ext
	  folder path/
	    file.ext

This folder is packaged together as a tar archive, with the
appropriate file extension:

	tar -cvf package.stf package

Or alternately, as a tar+gzip file:

	tar -zcvf package.stf.gz package
