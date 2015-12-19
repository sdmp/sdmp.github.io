---
layout: docs
title: request journal
subtitle: Requesting lists of journal entries.
---


Nodes transmit their own state to other [trusted](/trust) nodes
in the form of [journal updates](/journal/broadcast). However, the
node may also request the journal entries of another node.

---

## Request Object

When a journal update request is transmitted, it must be sent inside
of a valid [resource](/journal/resource) object, which must be signed
by the node sending the request.

---

## Description

Nodes request [journal entries](/journal/structure#journal-entries)
from other nodes.

This object contains the following properties:

###### `request` *(object, required)*

Holds the request properties.

###### `request.identifier` *(string, required)*

Responses to this request will include this identifier in the response
object. Implementations of this protocol should attempt to make this
property unique per request and per node, and should keep the value
within reasonable limits, such as under 128 characters.

###### `request.type` *(string, required)*

Indicates the request type. This must be the string `journal`.

###### `request.since` *(string, required)*

This property is the *last known*
[journal line identifier](/journal/structure#journal-line-identifier)
of the requesting node. A successful response to this request would have
the journal entries immediately after this entry, and would not include
this line identifier.

###### `request.limit` *(integer, optional)*

The *maximum* number of journal line identifier entries to include in the response. The
responding node may not exceed this limit, but if this value is not defined in the
request, the responding node may choose whatever limit it decides is appropriate.

---

## Schema

	{
	  "$schema": "http://json-schema.org/draft-04/schema#",
	  "type": "object",
	  "properties": {
	    "request": {
	      "type": "object",
	      "properties": {
	        "identifier": {
	          "type": "string"
	        },
	        "type": {
	          "type": "string",
	          "pattern": "^journal$"
	        },
	        "since": {
	          "type": "string"
	        },
	        "limit": {
	          "type": "integer",
	          "minimum": 1
	        }
	      },
	      "required": [ "identifier", "type", "since" ]
	    }
	  },
	  "required": [ "request" ]
	}
