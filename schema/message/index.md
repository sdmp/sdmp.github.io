---
layout: docs
title: message
subtitle: Text messages passed between users.
---


This schema is a simple schema to hold a human-readable message,
sent between two users.

---

## Description

This object contains the following property:

###### `message` *(string, required)*

The message content, which *must* be interpreted as
[MultiMarkdown](https://en.wikipedia.org/wiki/MultiMarkdown).

---

## Schema

	{
	  "$schema": "http://json-schema.org/draft-04/schema#",
	  "type": "object",
	  "properties": {
	    "message": {
	      "type": "string"
	    }
	  },
	  "required": [ "message" ]
	}
