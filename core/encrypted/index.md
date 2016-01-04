---
layout: docs
title: encrypted
subtitle: Store private data in a container object.
---


Private data is added to an SDMP container using well known
encryption techniques. Decryption of the payload must produce
a valid container object.

---

## Encryption

Encrypting an object is done by generating an [AES][aes] key, encrypting
the data to that key, and then encrypting the AES key to the public key of
all recipients.

The AES encrypted content is held in the `encrypted.payload` property,
while the RSA encrypted key is held in the `key` property of the recipient
object.

---

## Payload

The *payload* is an encoded representation of another
[container](/core/container) object.

Specifically, the payload is the output of the following function:

	BASE64URL(ENCRYPT(UTF8(JSON.stringify(SDMP Container))))

The child container is converted to a string, then [UTF-8](http://www.utf-8.com/)
encoded, then encrypted using the AES key, and then
[base64url][base64] encoded.

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

This object holds a signed string, which is the string representation
of a valid [container](/core/container) object.

This object contains the following properties:

###### `encrypted.iv` *(string, required)*

Initialization vector whose octets are [unpadded base64url][base64] encoded.

###### `encrypted.payload` *(string)*

The encrypted content, whose octets are [unpadded base64url][base64] encoded.

The encrypted content *must* be the `JSON.stringify` output of a fully valid
[container object](/core/container).

###### `encrypted.recipients` *(array of objects, required)*

Contains per-recipient information.

###### `encrypted.recipients[].key` *(string, required)*

Contains the key, encrypted to the recipient's public key, whose octets are
[unpadded base64url][base64] encoded.

---

## JWE Parameters

Describe crypto requirements for JWE object here.

[base64]: https://tools.ietf.org/html/rfc4648#section-5
[schema]: https://github.com/sdmp/sdmp-schema/blob/master/schemas/encrypted.json
[jwe]: http://self-issued.info/docs/draft-ietf-jose-json-web-encryption.htm
[jwe_serialize]: http://self-issued.info/docs/draft-ietf-jose-json-web-encryption.html#rfc.section.7.2
[aes]: https://en.wikipedia.org/wiki/Advanced_Encryption_Standard
