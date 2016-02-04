---
version: 0.13
parent: schema
layout: spec
title: user
subtitle: Published information about a user, such as a name.
---


The [identity object](../../core/identity) is used only for publishing
a public key. This object holds the personally identifiable
information about a user.

---

## Description

This JSON object holds information about the user.

This object contains the following properties:

###### `user` *(object)*

Contains information about the user.

###### `user.name` *(string)*

A human-readable name for the user.

###### `user.about` *(string)*

A human-readable description of the user. Meant to assist users in
differentiating between known users with similar names.

---

## Schema

	{
	  "$schema": "http://json-schema.org/draft-04/schema#",
	  "type": "object",
	  "properties": {
	    "user": {
	      "type": "object",
	      "properties": {
	        "name": { "type": "string" },
	        "about": { "type": "string" }
	      },
	      "required": [ "name" ]
	    }
	  },
	  "required": [ "user" ]
	}
