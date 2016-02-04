---
version: 0.13
parent: schema
layout: spec
title: receipt
subtitle: Used to notify another user that a message or resource was received.
---


When a user receives a message or private resource from another user,
they *should* send back a receipt of delivery to the original sender.

---

## Description

Used to notify another user that a message or resource was received.

This object contains the following properties:

###### `receipt` *(string, required)*

The [resource identifier](../../journal/resource#resource-identifier) of the
[message](../message) or [resource](../../journal/resource) received.

---

## Schema

	{
	  "$schema": "http://json-schema.org/draft-04/schema#",
	  "type": "object",
	  "properties": {
	    "receipt": {
	      "type": "string"
	    }
	  },
	  "required": [ "receipt" ]
	}
