---
title: cryptography
subtitle: Required cryptographic standards.
layout: page
---


The SDMP uses industry-standard cryptographic techniques to keep the network
traffic secure, and to strongly verify a users identity.

## RSA public/private keys

Where the phrase "public key", "private key", or "key pair" is used, it is meant
an asymmetric [RSA](https://en.wikipedia.org/wiki/RSA_(cryptosystem)) key pair.

The bit length of the RSA key **must** be at least 2048 bits.

## Hashing

Where the phrase "hash" or "digest" is used, it is meant the
[`SHA-256`](https://en.wikipedia.org/wiki/SHA-2) hashing algorithm
as specified in [FIPS PUB 180-2](http://csrc.nist.gov/publications/fips/fips180-2/fips180-2.pdf),
encoded to lower-case hexadecimal.

## Key hashing

Where a key hash is specified, it is meant the PGP fingerprint.

## PGP fingerprint

Where a PGP fingerprint is used, it is the complete V4 fingerprint as
specified in [RFC 4880](https://tools.ietf.org/html/rfc4880#section-12.2),
encoded to lower-case hexadecimal.

E.g., `cd92815bf6273acbaf834b9faed277c722068291`.

## AES encryption

Where the phrase "session key" is used, it is meant an
[AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)
compatible key of **at least** 256 bits.
