---
layout: docs
title: signature
subtitle: Generating and verifying signature objects.
---


Signature objects are [container](/core/container) objects containing
an encoded payload and signature data. This object is essentially a
[JWS (JSON Web Signature)][jws] object. The data being signed (in JWS
this is called the "payload") is the string representation of a container.

---

## Payload

The *JWS payload* (or simply *payload*) is an encoded representation
of another [container](/core/container) object.

Specifically, the payload is the output of the following function:

	BASE64URL(UTF8(JSON.stringify(SDMP Container)))

The child container is converted to a string, then [UTF-8](http://www.utf-8.com/)
encoded, and then [base64url][base64] encoded.

---

## Identifier

The signature object may be referenced elsewhere by the identifier, which
is the output of the [hashing algorithm](/core/cryptography#hashing)
applied to the payload, whose bytes have been [base64url][base64] encoded.

Specifically, the identifier is the output of the following function:

	HASH(BASE64URL(UTF8(JSON.stringify(SDMP Container))))

---

## Description

This object holds a signed string, which is the string representation
of a valid [container](/container) object.

This object contains the following properties:

###### `signature` *(object)*

Holds the signature object properties.

This contains the following reserved properties:

###### `signature.identifier` *(string, required)*

The [identifier](#identifier) of the payload.

###### `signature.payload` *(string, required)*

The resource [payload](#payload) string.

###### `signature.signatures` *(array of objects, required)*

Each object of this array must be a [JWS signature][jws_json_serialize] object.

Note that, unlike the JWS specifications, JWS objects containing a single
signature *must* still use this array.

###### `signature.signatures[].protected` *(string, required)*

Valid [JWS **protected** headers][jws_headers] for this signature. (Protected
headers are headers which have been [base64url][base64] encoded.)

The SDMP requires the following header values, and does not
allow additional values:

* `alg` : Must be exactly `HS512`
* `kid` : The [key fingerprint](/core/cryptography#key-fingerprint) of the
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

###### `signature.signatures[].signature` *(string, required)*

A valid JWS signature. This is the [HMAC SHA-512][hmac_sha2] hash, with
the bytes [base64url][base64] encoded, having been generated using the
key of the user or node signing the payload.

---

## Schema

	{
	  "$schema": "http://json-schema.org/draft-04/schema#",
	  "type": "object",
	  "properties": {
	    "signature": {
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
	  "required": [ "signature" ]
	}


[jws]: http://self-issued.info/docs/draft-ietf-jose-json-web-signature.html
[base64]: https://tools.ietf.org/html/rfc4648#section-5
[jws_json_serialize]: http://self-issued.info/docs/draft-ietf-jose-json-web-signature.html#GeneralJSONSerialization
[hmac_sha2]: https://tools.ietf.org/html/rfc7518#section-3.2
[jws_headers]: http://self-issued.info/docs/draft-ietf-jose-json-web-signature.html#rfc.section.4
