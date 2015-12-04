---
layout: docs
title: Cryptography
subtitle: Cryptographic standards used in the protocol.
---


The SDMP uses industry-standard cryptographic techniques to keep the network traffic
secure, to strongly encrypt messages, and to verify users and nodes identities.

---

## Keys

Where the phrase `public key`, `private key`, `key pair`, or `RSA key pair` is used, it
is meant that section or set of an asymmetric [RSA][w_rsa] key pair.

The bit length of the RSA key must be at least 2048 bits.

---

## Hashing

Where the phrase `hash` or `digest` is used, it is meant
the [SHA-512][w_sha2] hashing algorithm as specified in [FIPS PUB 180-2][fips180].

Where a string representation of a hash is used or specified, it is meant the output of
the SHA-512 hashing algorithm, whose octets are [unpadded base64url][base64] encoded.

---

## Key hashing

Where the phrase `key hash` is used, it is meant the hash of the raw component of the
public section of the RSA key pair.

---

## Key fingerprint

Where the phrase `key fingerprint` is used, it is meant the key hash of the public key.

E.g. a key fingerprint might look like:

	GlvAreTo0lCSyum7Wzh8pzhxYOOu-gMIgO2N95AAwAGP6-nR8xCvWvIW0t9rF_ZZfpCY_fDV38JDFKaOU91A8Q

---

## AES encryption

Where the phrase `session key` or `token key` is used, it is meant
an [AES][w_aes] compatible key.

The bit length of the session key must be at least 256 bits.

---

## Padding

When padding is specified, it is meant that the data must be padded with null bytes. This
is also often referred to as zero-byte padding. That is, the hexadecimal encoded value
of the bytes is simply `00`.


[w_rsa]: https://en.wikipedia.org/wiki/RSA_(cryptosystem)
[w_sha2]: https://en.wikipedia.org/wiki/SHA-2
[w_aes]: https://en.wikipedia.org/wiki/Advanced_Encryption_Standard
[fips180]: http://csrc.nist.gov/publications/fips/fips180-2/fips180-2.pdf
[base64]: https://tools.ietf.org/html/rfc4648#section-5
