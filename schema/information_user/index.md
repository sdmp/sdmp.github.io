---
layout: schema
title: information_user
subtitle: Published information about a user, such as a name.
---


The [identity object](/schema/identity) is used only for publishing
a public key. The information_user object is used as a public-facing
way to easily distinguish users.

---

## Description

This JSON object holds information about the user.

This object contains the following properties:

###### `information` *(object)*

Contains information about the user.

###### `information.type` *(string)*

Must be the exact string: `user`

###### `information.name` *(string)*

A human-readable name for the user.

###### `information.about` *(string)*

A human-readable description of the user. Meant to assiste users in differentiating between
known users with similar names.

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
