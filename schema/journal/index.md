---
layout: schema
title: journal
subtitle: Used by nodes to publish journal updates.
---


As part of the [synchronization](/synchronize/) process, nodes transmit
updates to their journal lists to other nodes. In this way, nodes can
transmit knowledge of a published resource without needing to transmit
the resource itself.

When a journal update is transmitted, it must be sent inside of a valid
[resource](/resource) container, signed by the node sending the update.

---

## Description

This schema is used when a node responds to a [journal request](/schema/request_journal),
or when a node periodically sends a journal update to other nodes.

This object has the following properties:

###### `journal` *(object, required)*

Holds the data properties. This contains the following reserved properties:

###### `journal.since` *(string, required)*

This property is the [journal line identifier](/journal#journal-line-identifier) of the line
immediately *preceding* the first entry in the journal entries list of this object.

(E.g., if the first entry of the journal entry list in this object were line
identifier `n`, this value would be `n-1`.)

###### `journal.entries` *(ordered string list, required)*

An ordered list of valid [journal entries](/journal#journal-entries).

---

## Schema

	{
	  "$schema": "http://json-schema.org/draft-04/schema#",
	  "type": "object",
	  "properties": {
	    "journal": {
	      "type": "object",
	      "properties": {
	        "since": {
	          "type": "string"
	        },
	        "entries": {
	          "type": "array",
	          "items": {
	            "type": "string"
	          }
	        }
	      },
	      "required": [ "since", "entries" ]
	    }
	  },
	  "required": [ "journal" ]
	}
