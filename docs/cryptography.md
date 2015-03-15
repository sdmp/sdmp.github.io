---
title: cryptography
subtitle: Required cryptographic standards.
layout: page
---


The SDMP uses industry-standard cryptographic techniques to keep the network
traffic secure, and to strongly verify a users identity.

## RSA public/private keys

Where the phrase "public key", "private key", or "key pair" is used, it is meant
an asymmetric [`RSA`](https://en.wikipedia.org/wiki/RSA_(cryptosystem)) key pair.

The bit length of the RSA key **must** be at least 2048 bits.

## Hashing

Where the phrase "hash" or "digest" is used, it is meant the
[`SHA-256`](https://en.wikipedia.org/wiki/SHA-2) hashing algorithm
as specified in [FIPS PUB 180-2](http://csrc.nist.gov/publications/fips/fips180-2/fips180-2.pdf),
serialized to `hex`.

## Key hashing

Where a key hash is specified, it is meant the PGP fingerprint.

## PGP fingerprint

Where a PGP fingerprint is used, it is the complete V4 fingerprint as
specified in [RFC 4880](https://tools.ietf.org/html/rfc4880#section-12.2).

## AES encryption

Where the phrase "message token" is used, it is mean an AES compatible key.

Where a resource is encrypted, it is done so using the
[AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)
cipher.

The key length used for AES encryption **must** be at least 256 bits.
