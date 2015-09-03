---
layout: schema
title: encrypted
subtitle: Private messages are held inside encrypted containers.
---


Publishing resources with encrypted content is supported by way of the
`encrypted` schema, which is essentially a [JSON Web Encryption(JWE)][jwe]
object. The encrypted content must be a valid [container](/schema/container/).

---

## Schema: `encrypted` ([JSON Schema][schema])

The `encrypted` schema is meant to describe the [JSON Web Encryption (JWE)][jwe]
object, using the [JWE JSON Serialization][jwe_serialize] method. Please refer to
the JWE specifications; any difference between the JWE and the following summary
should be considered [an error](https://github.com/sdmp/sdmp.github.io/issues).

The encrypted content *must* be a fully valid [container object](/schema/container/).

This object has the following properties:

###### `encrypted` *(object)*

The properties of this object must be a valid [JWE][jwe] object.

###### `encrypted.protected` *(string, required)*

Integrity protected shared header contents. This is equivalent to a `JSON.stringify`
of the header contents, whose octets are [unpadded base64url][base64] encoded.

###### `encrypted.unprotected` *(object, required)*

Shared header contents indicating the type of encryption. See the JWE
specifications for details.

###### `encrypted.aad` *(string)*

Additional authenticated data contents.

###### `encrypted.iv` *(string)*

Initialization vector whose octets are [unpadded base64url][base64] encoded.

###### `encrypted.tag` *(string)*

Authentication tag contents.

###### `encrypted.ciphertext` *(string)*

The encrypted content, whose octets are [unpadded base64url][base64] encoded.

The encrypted content *must* be the `JSON.stringify` of a fully valid
[container object](/schema/container/).

###### `encrypted.recipients` *(array of objects, required)*

Contains per-recipient information.

###### `encrypted.recipients[].header` *(object)*

Contains additional per-recipient unprotected header information.

###### `encrypted.recipients[].encrypted_key` *(string, required)*

Contains the recipient encrypted key whose octets are
[unpadded base64url][base64] encoded.


[base64]: https://tools.ietf.org/html/rfc4648#section-5
[schema]: https://github.com/sdmp/sdmp-schema/blob/master/schemas/encrypted.json
[jwe]: http://self-issued.info/docs/draft-ietf-jose-json-web-encryption.htm
[jwe_serialize]: http://self-issued.info/docs/draft-ietf-jose-json-web-encryption.html#rfc.section.7.2
