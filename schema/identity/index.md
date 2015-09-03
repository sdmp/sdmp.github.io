---
layout: schema
title: identity
subtitle: Establishes a cryptographic identity by publishing a public key.
---


To establish a cryptographically meaningful identity, the user or node
publishes a public key as a resource. The public key requirements are
established more completely [here](/cryptography/), but in summary the
public key is an RSA key of at least 2048 bits.

Note: Revoking an identity is done by publishing an identity update
without an identity schema in the [resource](/schema/resource).

---

## Schema: `identity` ([JSON Schema][schema])

This object holds the public key of a user or node, establishing their identity.

This object contains the following properties:

###### `identity` *(object)*

Holds the identity properties. This contains the following reserved properties:

###### `identity.type` *(string, required)*

Defines the type of identity. Must be one of the two values: `user` or `node`.

###### `identity.expires` *(string, required)*

After this timestamp, the identity must not be considered valid. The timestamp must
be in UTC, formatted using [ISO 8601][w_iso8601], with granularity to the millisecond.
E.g., `2015-07-26T15:48:37.703Z`.

Note: An application should not use this key after the date specified, and should
not consider resources signed with this key to be valid if they are believed to
be generated after this date.

###### `identity.key` *(string, required)*

The public key of a [globally](https://en.wikipedia.org/wiki/Earth) unique
[key-pair](/cryptography/#key-fingerprint), whose octets are encoded
as [unpadded base64url](https://tools.ietf.org/html/rfc4648#section-5).


[w_iso8601]: https://en.wikipedia.org/wiki/ISO_8601
[base64]: https://tools.ietf.org/html/rfc4648#section-5
[schema]: https://github.com/sdmp/sdmp-schema/blob/master/schemas/identity.json
