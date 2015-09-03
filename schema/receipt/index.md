---
layout: schema
title: receipt
subtitle: Used to notify another user that a message or resource was received.
---


When a user receives a message from another user, they *should* send back a
receipt to the original sender. Also, a user may wish to signal that they
received a resource. Both are acheived using a receipt object.

---

## Schema: `receipt` ([JSON Schema](https://github.com/sdmp/sdmp-schema/blob/master/schemas/receipt.json))

Used to notify another user that a message or resource was received.

This object contains the following properties:

###### `receipt` *(string, required)*

The [resource identifier](/resource/#resource-identifier) of the [message](/schema/message/)
or [resource](/resource/) received.
