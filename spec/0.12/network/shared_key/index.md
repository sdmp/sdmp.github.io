---
version: 0.12
parent: network
layout: spec
title: shared key
subtitle: Generate a shared symmetric key.
---

The SDMP uses the [Diffie-Hellman][w_dh] key exchange as the way to
establish session keys for [forward secrecy][w_forward] of network traffic.

---

## Diffie-Hellman Key Exchange

Recall that the generalized [Diffie-Hellman][w_dh] key exchange looks like:

1. Alice and Bob agree to use the values `p` and `g`
2. Alice chooses secret `a` and sends Bob `A`, where `A=(g^a)%p`
3. Bob chooses secret `b` and sends Alice `B`, where `B=(g^b)%p`
4. Alice computes `s`, where `s=(B^a)%p`
5. Bob computes `s`, where `s=(A^b)%p`
6. Alice and Bob share the secret `s`, which is the session key

---

## Description

This object contains the values necessary to establish a [Diffie-Hellman][w_dh]
key exchange.

The object contains the following properties:

###### `sdmp` *(object, required)*

This root property is part of the [container](../../core/container) object.

###### `sdmp.schemas` *(array of strings, required)*

Must be a *single array entry* with the string value: `connection`

Note that this means that placing any additional information or metadata in the
[container](../../core/container) object beyond what is specified for the `container`
and `connection` schema is considered an error.

###### `connection` *(object, required)*

Holds the parameters used in the Diffie-Hellman key exchange.

###### `connection.g` *(integer, required)*

The value `g` from the Diffie-Hellman key exchange.

###### `connection.p` *(string, required)*

The value `p` from the Diffie-Hellman key exchange, whose octets are
[unpadded base64url][base64] encoded.

###### `connection.N` *(string, required)*

The value `N` from the Diffie-Hellman key exchange (where `N=(g^n)%p`) whose
octets are [unpadded base64url][base64] encoded.

---

## Schema

	{
	  "$schema": "http://json-schema.org/draft-04/schema#",
	  "type": "object",
	  "properties": {
	    "sdmp": {
	      "type": "object",
	      "properties": {
	        "schemas": {
	          "type": "array",
	          "items": {
	          	"type": "string"
	          },
	          "minItems": 1,
	          "maxItems": 1
	        }
	      }
	    }
	    "connection": {
	      "type": "object",
	      "properties": {
	        "g": {
	          "type": "string"
	        },
	        "p": {
	          "type": "string"
	        },
	        "N": {
	          "type": "object"
	        }
	      },
	      "required": [ "g", "p", "N" ]
	    }
	  },
	  "required": [ "sdmp", "connection" ]
	}


[w_dh]: https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange
[w_forward]: https://en.wikipedia.org/wiki/Forward_secrecy
[base64]: https://tools.ietf.org/html/rfc4648#section-5
