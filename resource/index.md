---
layout: docs
title: Resource
subtitle: Published objects are called resources.
---


Within the SDMP, a *"resource"* or *"resource object"* is a
[JSON object](http://json.org/) which is an
[extension](/container#container-extension) of an
[SDMP container](/container) object. Every message published and
available within the SDMP must be a valid *resource* object.

The resource object is essentially a [JWS (JSON Web Signature)][jws]
object, with certain properties defined. The JWS payload is a
[container](/container) object, and is called *"child container"*.

---

## Definition

Where the term *"resource"* or *"resource object"* is used, it is meant a valid
[SDMP container](/container) with only a single entry in the `schemas` property,
which is the URI to the SDMP *resource* schema defined
[near the end](#json-schema) of this document.

Where the term *"[resource child container](#child-container)"* is used,
it is meant the JSON object which has been stringified and
[base64url][base64] encoded, and is in the *resource* container.

---

## JWS Payload

The *JWS payload*, or simply *payload*, of the resource is a
[base64url][base64] encoded representation of an [SDMP container](/container)
object, whose bytes have been [UTF-8](http://www.utf-8.com/)
encoded and *then* base64url encoded.

This inner container is called the *child container*, or
the *"resource child container"*.

E.g. for the container `SDMP Container`, the output of
`BASE64URL(UTF8(JSON.stringify(SDMP Container)))` is
the *payload* and `SDMP Container` is the *child container*.

The resource child container *must* be a valid [SDMP container](/container).

---

## Resource Identifier

The *resource identifer* is the output of having the bytes of the
*payload* string run through the *SHA-256* hashing algorithm,
and whose bytes are then [unpadded base64url][base64] encoded.

For example, a resource identifier might look like:

    h6FWguOHjaB5eUCsjeSIUGxA6p2abtY6HmaUin0F_9INc60VT3IELkP-q7IuWEwBCA8SpIbkfO9ZAIj5jgusYA

---

## Resource URI

All resources published within the SDMP *may* be referenced using a
[URI](https://tools.ietf.org/html/rfc3986) where only the *scheme name*,
*host*, and *path* are allowed:

* The *scheme name* is `sdmp`.
* The *host* is the [key fingerprint](/cryptography#key-fingerprint)
  of the *user* who published the resource.
* The *path* is the *resource identifier* of the resource.

For example, a URI which looks like `sdmp://<HOST>/<PATH>`, e.g.

    sdmp://GlvAreTo0lCSyum7Wzh8pzhxYOOu-gMIgO2N95AAwAGP6-nR8xCvWvIW0t9rF_ZZfpCY_fDV38JDFKaOU91A8Q/h6FWguOHjaB5eUCsjeSIUGxA6p2abtY6HmaUin0F_9INc60VT3IELkP-q7IuWEwBCA8SpIbkfO9ZAIj5jgusYA

would have the following parts:

* *schema name*: `sdmp`
* *host*: `GlvAreTo0lCSyum7Wzh8pzhxYOOu-gMIgO2N95AAwAGP6-nR8xCvWvIW0t9rF_ZZfpCY_fDV38JDFKaOU91A8Q`
* *path*: `h6FWguOHjaB5eUCsjeSIUGxA6p2abtY6HmaUin0F_9INc60VT3IELkP-q7IuWEwBCA8SpIbkfO9ZAIj5jgusYA`

---

## Interpreting URI References

References of the form `sdmp://<HOST>/<PATH>` should be interpreted to
be a reference to the resource published by the user identified by `<HOST>`
and having a [resource identifier](#resource-identifier) of `<PATH>`.

References of the form `sdmp://<HOST>` should be interpreted to be a
reference to the [identity resource](/schema/identity) of the user identified by
the `<HOST>` value.

Note that this is equivalent to a request of the form `sdmp://<HOST>/<PATH>`
where `<PATH>` is the resource identifier of the identity resource.

E.g., if `h6FWg...jgusYA` references the [identity resource](/schema/identity)
for the user `GlvAre...U91A8Q`, the following URIs would be equivalent:

    sdmp://GlvAre...U91A8Q/
    sdmp://GlvAre...U91A8Q/h6FWg...jgusYA

---

## Description

This object holds a signed string, which is the string representation
of a valid [container](/container) object.

This object contains the following properties:

###### `resource` *(object)*

Holds the resource properties. This contains the following reserved properties:

###### `resource.identifier` *(string, required)*

The [resource identifier](#resource-identifier) of this resource.

###### `resource.payload` *(string, required)*

The resource [payload](#jws-payload) string.

###### `resource.signatures` *(array of objects, required)*

Each object of this array must be a [JWS signature][jws_json_serialize] object.

Note that, unlike the JWS specifications, JWS objects containing a single
signature *must* still use this array.

###### `resource.signatures[].protected` *(string, required)*

Valid [JWS **protected** headers][jws_headers] for this signature. (Protected
headers are headers which have been [base64url][base64] encoded.)

The SDMP requires the following header values, and does not
allow additional values:

* `alg` : Must be exactly `HS512`
* `kid` : The [key fingerprint](/cryptography#key-fingerprint) of the
  user or node which generated this signature.

For example, if the key fingerprint of the node generating the signature is:

	GlvAreTo0lCSyum7Wzh8pzhxYOOu-gMIgO2N95AAwAGP6-nR8xCvWvIW0t9rF_ZZfpCY_fDV38JDFKaOU91A8Q

Than the header *object* would look like (newlines added for readability only):

	{
		"alg": "HS512",
		"kid": "GlvAreTo0lCSyum7Wzh8pzhxYOOu-gMIgO2N95AAwAGP6-nR8xCvWvIW0t9rF_ZZfpCY_fDV38JDFKaOU91A8Q"
	}

And the protected header string would be:

	und63q6WhN8Q3v9_mGoIy66f70Nq21jMoBwAD4W5oxuxMth8L-nN2GKeV6XjBAE_KbrNs7_MdegCNOD8uUSqJw

###### `resource.signatures[].signature` *(string, required)*

A valid JWS signature. This is the [HMAC SHA-512][hmac_sha2] hash, with
the bytes [base64url][base64] encoded, having been generated using the
key of the user or node signing the resource.

---

## Schema

	{
	  "$schema": "http://json-schema.org/draft-04/schema#",
	  "type": "object",
	  "properties": {
	    "resource": {
	      "type": "object",
	      "properties": {
	        "identifier": {
	          "type": "string"
	        },
	        "payload": {
	          "type": "string"
	        },
	        "signatures": {
	          "type": "array",
	          "items": {
	            "type": "object",
	            "properties": {
	              "protected": {
	                "type": "string"
	              },
	              "signature": {
	                "type": "string"
	              }
	            },
	            "required": [ "protected", "signature" ]
	          }
	        }
	      },
	      "required": [ "identifier", "payload", "signatures" ]
	    }
	  },
	  "required": [ "resource" ]
	}


[jws]: http://self-issued.info/docs/draft-ietf-jose-json-web-signature.html
[base64]: https://tools.ietf.org/html/rfc4648#section-5
[jws_json_serialize]: http://self-issued.info/docs/draft-ietf-jose-json-web-signature.html#GeneralJSONSerialization
[hmac_sha2]: https://tools.ietf.org/html/rfc7518#section-3.2
[jws_headers]: http://self-issued.info/docs/draft-ietf-jose-json-web-signature.html#rfc.section.4
