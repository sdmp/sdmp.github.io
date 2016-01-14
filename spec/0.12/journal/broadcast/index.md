---
version: 0.12
parent: journal
layout: spec
title: broadcast
subtitle: Broadcast journal updates to other nodes.
---


As part of the synchronization process, nodes transmit journal
updates to other nodes. In this way, nodes can transmit knowledge
of a published [resource](../../journal/resource) without needing to
transmit the resource itself.

---

## Broadcast Object

When a journal update is transmitted, it must be sent inside of
a valid [resource](../../journal/resource) object, which must be signed
by the node sending the update.

---

## Description

This schema is used when a node responds to a
[journal request](../../journal/request_journal),
or when a node periodically sends a journal update
to other nodes.

The deserialized [resource payload](../../journal/resource#payload) object
contains the list of [journal entries](../../journal/structure#journal-entries)
that it has added since the last time it broadcast to the particular node.

For example, if node `A` broadcasts a journal update to node `B` and
then writes `n` additional entries to its own journal, node `A` would
broadcast those `n` entries to node `B` on the next broadcast.

This object has the following properties:

###### `journal` *(object, required)*

Holds the data properties. This contains the following reserved properties:

###### `journal.since` *(string, required)*

This property is the [journal line identifier](../../journal/structure#journal-line-identifier)
of the line immediately *preceding* the first entry in the journal entries list of this object.

(E.g., if the first entry of the journal entry list in this object were line
identifier `n`, this value would be `n-1`.)

###### `journal.entries` *(ordered string list, required)*

An ordered list of valid [journal entries](../../journal/structure#journal-entries).

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
