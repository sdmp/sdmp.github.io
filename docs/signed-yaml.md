---
title: signed yaml
subtitle: Modification of YAML file syntax to include signature.
layout: page
---


This is a technical descriptor of a constructed file which is a
[YAML][yaml] stream that has been signed using a public/private
key (e.g. `RSA`).

## YAML Requirements

A valid `YAML` stream (`1.0`, `1.1`, or `1.2`) is first constructed, with
the following requirements:

1. The `YAML` stream **must** start with three dashes (e.g., `---`) as shown
	in [YAML 1.0][yaml0dash], [YAML 1.1][yaml1dash], and [YAML 1.2][yaml2dash].
2. The `YAML` stream **must** be terminated with the three periods
	(e.g. `...`) as shown in [YAML 1.0][yaml0dots], [YAML 1.1][yaml1dots],
	and [YAML 1.2][yaml2dots].
3. Characters prior to the initiating three dashes (`---`) and after
	the three periods (`...`) **are not** allowed.
4. `YAML` multi-document streams **are** allowed, so long as they conform
	to the other requirements of this document.

## Message Digest

A message digest is generated from the characters in the `YAML` stream,
including comments, and including the starting dashes (`---`) and the
ending three periods (`...`).

The message digest used **must** be the `SHA-256` hash as defined
in [FIPS PUB 180-2][fips].

## Digest Signature

The message digest **must** be signed using a private `RSA` key of
**at least** `2048` bits.

## Digest signature encoding

The binary data of the digest signature is serialized using the base64 format
as defined by [RFC 2045][rfc2045] (MIME), with the following changes:

* The content is **not** restricted to lines of 76 characters or less.
* Characters other than the base64 alphabet, line breaks, and white
	space are considered **an error**.

## Constructing the SYML Stream

The `base64` encoded digest signature is prefixed to the `YAML`
stream and optionally split at some character length with the
*carriage return* **and** *newline* characters (in regex: `\r\n`).

There is no required length at which the characters must be
split, but it is **recommended** that they be split before or
at `80` characters.

After the final character of the digest signature, an additional set
of *carriage return* **and** *newline* characters **must** be inserted.

## Final Constructed Stream

The final `SYML` stream has the characters ordered as follows:

1. The `base64` encoded digest signature, split at some character
	length with the characters `\r\n`.
2. After the last digest signature character, the `\r\n` characters.
3. The three dashes (`---`) indicating the start of the `YAML` stream.
4. The remainder of the `YAML` stream, finalized with the three
	periods (`...`) marking the end of the stream.

## Signature Verification

To verify the signature of the `SYML` file, the message digest is
generated, and this digest is verified using the public key of the
key-pair used to sign the `YAML` stream.

## File Integrity

Implementations of the `SYML` file readers **should** include the
signature verification as part of the file opening process. If the
signature is found to be invalid, or if the application opening the
file does not have the public key required for proper verification,
this is a security risk and the user **should** be alerted or informed
wherever possible.

## Example

An example of a `SYML` file might look like:

	OS04M3FodDAtOGhxLTlyOGhnLTlhOGhmZy05c2RmaGdqWpkZjA5amFzZGYw
	OWphMDlqZjA5amRmc2QAtOGhxLTlyOGhnLTlhOGhmZy05c2RmaGdqOS04M3
	FodDjA5amFzZGYwOWphMDlqZjA5amRmc2QWpkZ5c2RmaGdqOS04M3FodDAt
	ZWZpb2pzMDlkZnUtMGVqZmFwc29kamZhczlkZmMGFzOjBqZjA5MmplMGY5a
	mEwczlqZGYwOWphczA5ZGpjBqZjA5MmplMGY5amEwczlqZGYwOWphczA5ZG
	pmMGFzO5amFzZGYwOWphMDlqZjA5amRmc2QWpkZjA=
	---
	receipt: Oz-Ware Purchase Invoice
	date:    2012-08-06
	customer:
	  given:   Dorothy
	  family:  Gale
	...

[yaml]: http://www.yaml.org/
[yaml0dots]: http://www.yaml.org/spec/1.0/#id2489959
[yaml1dots]: http://www.yaml.org/spec/1.1/#id857577
[yaml2dots]: http://www.yaml.org/spec/1.2/spec.html#id2760395
[yaml0dash]: http://yaml.org/spec/1.0/#id2489959
[yaml1dash]: http://yaml.org/spec/1.1/#id857577
[yaml2dash]: http://www.yaml.org/spec/1.2/spec.html#id2760395
[fips]: http://csrc.nist.gov/publications/fips/fips180-2/fips180-2.pdf
[radix64]: http://tools.ietf.org/html/rfc4880
[rfc2045]: http://tools.ietf.org/html/rfc2045
