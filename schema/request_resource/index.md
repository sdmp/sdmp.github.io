---
layout: schema
title: request_resource
subtitle: Nodes requesting specific published resources from other nodes.
---


Because [synchronization](/synchronization) is not immediate, when a
user publishes a [resource](/resource) the resource object may not be
transmitted to all nodes. At a later time, a node may request a
resource from another node using this request object.

---

## Description

Used to request a specific resource from another node.

This object contains the following properties:

###### `request` *(object, required)*

Holds the request properties.

###### `request.type` *(string, required)*

Indicates the request type. This must be the string `resource`.

###### `request.identifier` *(string, required)*

Responses to this resource request will include this identifier in the response
object. Implementations of the SDMP should attempt to make this property unique
per request and per node, and should keep the value within reasonable limits, such
as under 128 characters.

###### `request.author` *(string, required)*

The [key fingerprint](/cryptography#key-fingerprint) of the *user* who published
the resource being requested.

###### `request.resource` *(string, required)*

The [resource identifier](/resource#resource-identifier) of the resource being requested.

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
