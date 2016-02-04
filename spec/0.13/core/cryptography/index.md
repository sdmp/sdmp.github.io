---
version: 0.12
parent: core
layout: spec
title: Cryptography
subtitle: Cryptographic standards used in the protocol.
---


The SDMP uses industry-standard cryptographic techniques to keep the network traffic
secure, to strongly encrypt messages, and to verify users and nodes identities.

---

## Keys

Where the phrase `public key`, `private key`, `key pair`, or `RSA key pair` is
used, it is meant that section or set of an asymmetric [RSA][w_rsa] key pair.

The bit length of the RSA key must be *at least* 2048 bits.

---

## Hashing

Where the phrase `hash` or `digest` is used, it is meant
the [SHA-512][w_sha2] hashing algorithm as specified in [FIPS PUB 180-2][fips180].

Where a string representation of a hash is used or specified, it is meant the output of
the SHA-512 hashing algorithm, whose octets are [unpadded base64url][base64] encoded.

The hashing of keys is done by hashing the string value of the `identity.key`
property of the [identity](../identity) resource.

---

## Key fingerprint

Where the phrase `key fingerprint` is used, it is meant the key hash of the public
key, encoded to [base64url][base64].

---

## Key hashing

Where the phrase `key hash` is used, it is meant the generated from the UTF-8
encoded `identity.key` property of the [identity](../identity) resource.

E.g., for this example public key:

	-----BEGIN PUBLIC KEY-----
	MIIBIzANBgkqhkiG9w0BAQEFAAOCARAAMIIBCwKCAQIA2IxlYprmk/QpH1Rsz+WQ
	m+WUYqR2xlV+xXDDOesMkwja32aUsjYe1kCS6nTqazEdy4m9utF3Eb3K5i8crTeg
	g3GE+iJvYAgGXvMLiAb/Vrj0OqKv8m0BoZnxW7AjCURZ/tDn/YxNk5qnzhZLLMIT
	zHQlZsKmBp/ZlrhTr4VgKxKlT4z/NNTPMnLHyFB4XVR6ge20nNWN6RJ03kk85+QI
	mCGPU9Us70H6FQYI/e0Iq+64gw8PgpDZP+RSzYvpAr7alCj4/f0jeFzrz+lpsIzY
	95bk2ucfasPYkH7m6UP1EmBfZJUn+pnX3CPpeex1h5RCKw4DoMSRgXrfoVjYv80w
	QfUCAwEAAQ==
	-----END PUBLIC KEY-----

The property of `identity.key` would be:

	-----BEGIN PUBLIC KEY-----\nMIIBIzANBgkqhkiG9w0BAQEFAAOCARAAMIIBCwKCAQIA2IxlYprmk/QpH1Rsz+WQ\nm+WUYqR2xlV+xXDDOesMkwja32aUsjYe1kCS6nTqazEdy4m9utF3Eb3K5i8crTeg\ng3GE+iJvYAgGXvMLiAb/Vrj0OqKv8m0BoZnxW7AjCURZ/tDn/YxNk5qnzhZLLMIT\nzHQlZsKmBp/ZlrhTr4VgKxKlT4z/NNTPMnLHyFB4XVR6ge20nNWN6RJ03kk85+QI\nmCGPU9Us70H6FQYI/e0Iq+64gw8PgpDZP+RSzYvpAr7alCj4/f0jeFzrz+lpsIzY\n95bk2ucfasPYkH7m6UP1EmBfZJUn+pnX3CPpeex1h5RCKw4DoMSRgXrfoVjYv80w\nQfUCAwEAAQ==\n-----END PUBLIC KEY-----

And the hash (encoded to base64url) would be:

	B_3ji9cDp1SJTyPnxuslQ66IT3zwEf4TvTEnGo60_2KGANVfhRY0XA2crFKIfq6edYcexkMYzfz6y3UsPZZLtw

---

## AES encryption

Where the phrase `session key` or `token key` is used, it is meant
an [AES][w_aes] compatible key. Encryption must use the AES-CBC algorithm.

The bit length of the session key must be 256 bits.

---

## Padding

When padding is specified, it is meant that the data must be padded with null bytes. This
is also often referred to as zero-byte padding. That is, the hexadecimal encoded value
of the bytes is simply `00`.

---

## Padding Length

When padding an encrypted object, it must be padded to the nearest `8192` bytes.


[w_rsa]: https://en.wikipedia.org/wiki/RSA_(cryptosystem)
[w_sha2]: https://en.wikipedia.org/wiki/SHA-2
[w_aes]: https://en.wikipedia.org/wiki/Advanced_Encryption_Standard
[fips180]: http://csrc.nist.gov/publications/fips/fips180-2/fips180-2.pdf
[base64]: https://tools.ietf.org/html/rfc4648#section-5
