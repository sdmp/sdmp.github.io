---
layout: schema
title: message
subtitle: Text messages passed between users.
---


This is a very small schema, it is used in the case of two users communicating.
Message objects are typically inside an [encrypted container](/schema/encrypted/).

---

## Schema: `message` ([JSON Schema][https://github.com/sdmp/sdmp-schema/blob/master/schemas/message.json])

For convenience, the SDMP defines this simple message schema, which is typically
used inside [encrypted containers](/schema/encrypted/) as messages between users.

This object contains the following properties:

###### `message` *(string, required)*

The message content, which *must* be formatted as
[MultiMarkdown](https://en.wikipedia.org/wiki/MultiMarkdown).
