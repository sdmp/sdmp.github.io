---
title: uri scheme
subtitle: Linking to other resources.
layout: page
---


# URI Scheme

The [URI scheme](https://en.wikipedia.org/wiki/URI_scheme) for linking
to sdmp messages is formed in the following manner:

	sdmp://[<CONNECTION>@]<AUTHOR>[/<MESSAGE>]

* The URI scheme name is `sdmp`.
* `AUTHOR` is the [hash](./cryptography) of the public key of the user who authored the resource.
* `CONNECTION` is optional and is the hash of a connection or node public key.
* `MESSAGE` is optional and is the hash of the [message](./message).

## URI forms

### sdmp://\<AUTHOR>/

If requests of this form are successful, they will yield the `user-create`
[control stream](./control) of the user whose public key hash is `AUTHOR`.

## sdmp://\<AUTHOR>/\<MESSAGE>

If requests of this form are successful, they will yield the message
whose hash is the `MESSAGE`, which has been published by the user
whose public key hash is `AUTHOR`.

## sdmp://\<CONNECTION>@\<AUTHOR>/

If requests of this form are successful, they will yield the `connection-add`
control stream created by the user whose public key hash is `AUTHOR`,
for the connection whose public key hash is `CONNECTION`.
