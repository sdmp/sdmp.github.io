---
version: 0.12
parent: journal
layout: spec
title: request resource
subtitle: Nodes requesting specific published resources from other nodes.
---


Nodes transmit [journal updates](../../journal/broadcast) to other nodes,
but do not automatically transmit the [resources](../../journal/resource).
It is the responsibility of the node receiving the journal update to
request resources it does not have.

---

## Request Object

When a resource request is transmitted, it must be sent inside of
a valid [resource](../../journal/resource) object, which must be signed
by the node sending the request.

---

## Description

Used to request a specific resource from another node.

This object contains the following properties:

###### `request` *(object, required)*

Holds the request properties.

###### `request.type` *(string, required)*

Indicates the request type. This must be the string `resource`.

###### `request.identifier` *(string, required)*

Responses to this request will include this identifier in the response
object. Implementations of this protocol should attempt to make this
property unique per request and per node, and should keep the value
within reasonable limits, such as under 128 characters.

###### `request.author` *(string, required)*

The [key fingerprint](../../core/cryptography#key-fingerprint) of the *user*
who published the resource being requested.

###### `request.resource` *(string, required)*

The [resource identifier](../../journal/resource#resource-identifier) of
the resource being requested.

---

## Schema

	{
	  "$schema": "http://json-schema.org/draft-04/schema#",
	  "type": "object",
	  "properties": {
	    "request": {
	      "type": "object",
	      "properties": {
	        "type": {
	          "type": "string",
	          "pattern": "^resource$"
	        },
	        "identifier": {
	          "type": "string",
	          "pattern": ".{0,40}"
	        },
	        "author": {
	          "type": "string"
	        },
	        "resource": {
	          "type": "string"
	        }
	      },
	      "required": [ "type", "identifier", "author", "resource" ]
	    }
	  },
	  "required": ["request"]
	}
