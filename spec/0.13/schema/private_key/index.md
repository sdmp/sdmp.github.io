---
version: 0.13
parent: schema
layout: spec
title: private_key
subtitle: Store the private key of an identity.
---


Creating an [identity](../../core/identity) involves generating
a private/public key pair. To make cross-application development
easier, a container is specified to hold the private section of
the identity key pair.

---

## Publishing Not Allowed

Private keys **must not** be published as resources, even if those
private keys are password protected, and implementations should not
allow private keys to be published.

---

## Description

This object holds the private key of a user or node.

This object contains the following properties:

###### `private_key` *(object)*

Holds the private key section of an identity.

This object contains the following reserved properties:

###### `private_key.expires` *(string, required)*

This timestamp should match the timestamp in the [identity](../../core/identity)
resource, which is the public section of the key. After this timestamp, the
identity must not be considered valid and the private key must not be used.

The timestamp must be in UTC, formatted using [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601),
with granularity to the millisecond. E.g., `2015-07-26T15:48:37.703Z`.

###### `private_key.key` *(string, required)*

The private section of a key pair used to generate the identity resource.
This is equivalent to a normal OpenSSL PEM encoded key. A password is *not*
required.

For example, a password protected PEM encoded key might look like
(truncated with ellipsis for readability):

	-----BEGIN RSA PRIVATE KEY-----
	Proc-Type: 4,ENCRYPTED
	DEK-Info: DES-EDE3-CBC,1842B0A85FAE341F

	f8NHTCHC2vA09bzbOxIWWeO5XCdhnh0Uoo8KSQ4rjM3uTnyOCnNBAv/Tv7XpDVaI
	Dqq/3pIY/RDXOrpwUBf/I+46HjHdvZPUZ4WJYijgm0/1KsrrBnutQ2jWibqthEe9
	J9HsX8Ke5YiLA1U++OCP4g6UVhmiFFpQvVQxzvEnfkNs4UDyf0C/khLNKiMwe7oo
	lt8/fplW67ZI9Z7JdajruUIvYJBu6YzSCl7qJ2dwrIE=
	-----END RSA PRIVATE KEY-----

Or a PEM encoded key without password protection might look like:

	-----BEGIN RSA PRIVATE KEY-----
	MIGqAgEAAiEAxLxAvEN9GfYWyhOUtpYs/RPCeu0qBhBcfb+fUaTMRMUCAwEAAQIg
	ZpJkCgQB4Jw+i4MkAQJRV4LxVYws1J3orXGgvjFJ/k0CEQDw20d0CgMkmkiCm+Vx
	HJq3AhEA0RrRhjBFXvaqXl/mXncQYwIQLiJCQHa+fV9T7jiALTdGZwIRAM8dIiUo
	kL2T0oHxuQseIYMCEEK636MDVkNlrt5VMjLNwZY=
	-----END RSA PRIVATE KEY-----

---

## Schema

	{
	  "$schema": "http://json-schema.org/draft-04/schema#",
	  "type": "object",
	  "properties": {
	    "private_key": {
	      "type": "object",
	      "properties": {
	        "expires": { "type": "string" },
	        "key": { "type": "string" },
	        "fingerprint": { "type": "string" }
	      },
	      "required": [ "expires", "key", "fingerprint" ]
	    }
	  },
	  "required": [ "private_key" ]
	}


[w_iso8601]: https://en.wikipedia.org/wiki/ISO_8601
[base64]: https://tools.ietf.org/html/rfc4648#section-5
