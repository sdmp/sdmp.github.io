---
layout: docs
title: identity
subtitle: Establish a cryptographic identity by publishing a public key.
---


Establishing a cryptographic identity is done by creating and publishing
a [public key](/core/cryptography) as a [resource](/journal/resource).

When published as a resource, the resource must be signed by the private
key of this identity key pair. 

---

## Description

This object holds the public key of a user or node, establishing their identity.

This object contains the following properties:

###### `identity` *(object)*

Holds the identity properties. This contains the following reserved properties:

###### `identity.type` *(string, required)*

Defines the type of identity. Must be one of the two values: `user` or `node`.

###### `identity.expires` *(string, required)*

After this timestamp, the identity must not be considered valid. The timestamp must
be in UTC, formatted using [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601), with
granularity to the millisecond. E.g., `2015-07-26T15:48:37.703Z`.

Note: An application should not use this key after the date specified, and should
not consider resources signed with this key to be valid if they are believed to
be generated after this date.

###### `identity.key` *(string, required)*

The public key of a unique [key-pair](/core/cryptography#keys), which is
equivalent to a normal OpenSSL PEM encoded key.

For example, given a PEM encoded key (truncated with ellipsis
for readability):

	-----BEGIN PUBLIC KEY-----
	MIIBIjAN...9wIDAQAB
	-----END PUBLIC KEY-----

The string stored in `identity.key` would be:

	-----BEGIN PUBLIC KEY-----\nMIIBIjAN...9wIDAQAB\n-----END PUBLIC KEY-----

###### `identity.fingerprint` *(string, required)*

The [key fingerprint](/core/cryptography#key-fingerprint) of the public key
in this object, whose octets are encoded as [unpadded base64url][base64].

---

## Schema

	{
	  "$schema": "http://json-schema.org/draft-04/schema#",
	  "type": "object",
	  "properties": {
	    "identity": {
	      "type": "object",
	      "properties": {
	        "type": { "enum": [ "user", "node" ] },
	        "expires": { "type": "string" },
	        "key": { "type": "string" },
	        "fingerprint": { "type": "string" }
	      },
	      "required": [ "type", "expires", "key", "fingerprint" ]
	    }
	  },
	  "required": [ "identity" ]
	}


[w_iso8601]: https://en.wikipedia.org/wiki/ISO_8601
[base64]: https://tools.ietf.org/html/rfc4648#section-5
