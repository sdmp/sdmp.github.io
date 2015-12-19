---
layout: docs
title: encrypted
subtitle: Store private data in a <a href="/container">container</a> object.
---


Private data is added to an SDMP container using the JSON Web
Encryption ([JWE][jwe]) specifications. Decryption of the JWE
object ciphertext must produce a valid container object.

---

## Encryption

Encrypting an object is done by generating an [AES][aes] key, encrypting
the data to that key, and then encrypting the AES key to the public key of
all recipients.

The AES encrypted content is held in the `encrypted.ciphertext` property,
while the RSA encrypted key is held in the `key` property of the recipient
object.

---

## Decryption

The RSA encrypted key is decrypted using the recipient's private key,
and this key is used to decrypt the AES encrypted content.

---

## Description

This object is an extension of the [container object](/core/container),
so it must also have all properties required in the container object
specifications.

In addition, the object also has the following properties:

###### `encrypted` *(object, required)*

This root property contains a valid [JWE][jwe] object. The properties of this
object are defined using the JWE [JSON Serialization][jwe_serialize] method.

> Note: Defining the complete JWE is outside the scope of this document, but the
> required properties are listed here to summarize the JWE specifications. Any
> difference between the JWE and the following summary should be considered an
> error, and should be filed as [a bug](https://github.com/sdmp/sdmp.github.io/issues).

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

The encrypted content *must* be the `JSON.stringify` output of a fully valid
[container object](/schema/container/).

###### `encrypted.recipients` *(array of objects, required)*

Contains per-recipient information.

###### `encrypted.recipients[].header` *(object)*

Contains additional per-recipient unprotected header information.

###### `encrypted.recipients[].key` *(string, required)*

Contains the recipient encrypted key whose octets are
[unpadded base64url][base64] encoded.

---

## JWE Parameters

Describe crypto requirements for JWE object here.

[base64]: https://tools.ietf.org/html/rfc4648#section-5
[schema]: https://github.com/sdmp/sdmp-schema/blob/master/schemas/encrypted.json
[jwe]: http://self-issued.info/docs/draft-ietf-jose-json-web-encryption.htm
[jwe_serialize]: http://self-issued.info/docs/draft-ietf-jose-json-web-encryption.html#rfc.section.7.2
[aes]: https://en.wikipedia.org/wiki/Advanced_Encryption_Standard
