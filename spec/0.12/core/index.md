---
version: 0.12
layout: spec
title: core
subtitle: Core objects and definitions.
---


The SDMP makes use of RSA keys, transferred inside JSON objects, which
are defined using JSON Schema. Similarly, signed and encrypted data is
stored inside defined JSON objects.


## [Cryptography](./cryptography)

Key pairs are generated using [RSA][rsa], asymmetric keys are generated
using [AES][aes], and hashing uses [SHA-512][sha2]. String representations
of hashes are encoded as [base64url][base64].

## [Container](./container)

Every object inside the SDMP is a type of container. A container is a JSON
object which references [JSON Schemas][jsonschema], which describe what type
of data is in the object. The schemas are also used to validate the object.

## [Identity](./identity)

There are two identities: users and nodes. Users authorize nodes to publish
content. An identity is the container object with the public key of the user
or node. Users authorize nodes to publish content by signing a trust.

## [Encryption/Decryption](./encrypted)

Encryption of an object is done by generating an [AES][aes] key, encrypting
the data to that key, and then encrypting the AES key to the public key of
all recipients. Decryption is the reverse.

## [Sign/Verify](./signature)

RSA signatures are stored in containers. The signed data must be a container
object.

[rsa]: https://en.wikipedia.org/wiki/RSA_(cryptosystem)
[sha2]: https://en.wikipedia.org/wiki/SHA-2
[aes]: https://en.wikipedia.org/wiki/Advanced_Encryption_Standard
[base64]: https://tools.ietf.org/html/rfc4648#section-5
[jsonschema]: http://json-schema.org/
