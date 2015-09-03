---
layout: schema
title: information_user
subtitle: Published information about a user, such as a name.
---


The [identity object](/schema/identity/) is primarily used for publishing
a public key, but this object is used as a public-facing, human-readable
way to easily distinguish users.

---

## Schema: `information_user` ([JSON Schema][schema])

Information about the user is *not* published in the [identity object](/schema/identity/),
it is published in this object.

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


[schema]: https://github.com/sdmp/sdmp-schema/blob/master/schemas/information_user.json
