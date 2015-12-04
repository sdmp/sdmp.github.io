---
layout: schema
title: relationship
subtitle: Indicates a trust level between nodes and users.
---


The trust relationship between users and between nodes is established
using the *relationship* schema. A trusted user is one for which you
have published a relationship resource indicating a trust. A
trusted node is a node that is trusted by a trusted user.

---

## Relationship Types

Three relationship types are defined:

### Connection *(user to user)*

When a user indicates that a second user is a "connection", it means
that the second user may receive requested resources.

For example: If Bob makes Alice a connection, than if Alice requests
a resource published by Bob from Jane, than Jane should respond with
that resource.

### Publisher *(user to node)*

When a user indicates that a node is a "publisher", it means that the
node may publish resources on behalf of the user.

For example: a user might install an app on their smart phone and then
indicate that this app operates as a publisher node, meaning that the
user does not need their main key-pair to be accesible on that device
to publish resources.

### Host *(user to node)*

When a user indicates that a node is a "host", it means that the node
may send and request resources on behalf of the user, but it may not
publish new resources on behalf of the user.

This would be useful for shared hosting always-on computers or servers,
which are trusted enough to synchronize encrypted messages, but not
trusted enough to publish messages on a users behalf.

---

## Description

This object contains the following properties:

###### `relationship` *(object)*

Object holding information about the relationship.

###### `relationship.type` *(string, required)*

Defines the type of relationship.

Must be one of the following: `connection`, `publisher`, `host`.

###### `relationship.identity` *(string, required)*

The [key fingerprint](/cryptography#key-fingerprint) of the user or node to
which the relationship is being created.

---

## Schema

	{
	  "$schema": "http://json-schema.org/draft-04/schema#",
	  "type": "object",
	  "properties": {
	    "relationship": {
	      "type": "object",
	      "properties": {
	        "type": {
	          "enum": [ "connection", "host", "publisher" ]
	        },
	        "identity": {
	          "type": "string",
	          "pattern": "^[a-zA-Z0-9_-]+$"
	        }
	      }
	    }
	  },
	  "required": [ "relationship" ]
	}
