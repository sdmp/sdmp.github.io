---
title: message
subtitle: Node-to-node message contruction and transmission.
layout: page
---


Messages between nodes are signed YAML ([SYML](./signed-yaml)) streams, encrypted
using the session key established during the [connection](./connect) procedure.

## Message padding

Prior to encryption, the SYML stream is zero-byte padded to the nearest `4096` byte block.

## YAML restrictions

All messages **must** be valid [SYML](./signed-yaml) streams, with the following additional
restrictions:

* Multi-document YAML streams are **not** allowed.
* Comments inside the YAML stream are **not** allowed.
* More precisely, **only** values which are actual YAML properties or values are allowed.
* A single property at the root of the YAML document named `message` is **required** and **reserved**.
* It is **recommended** that properties not specified in this document not be placed in the `message` object.

## Properties of the `message` object

The following properties of the `message` object are reserved for use within the SDMP:

### `user` (string, required)

The node sending a message **must** include the [key fingerprint](./cryptography) of
the user that has authorized the node to create messages.

### `action` (string, required)

The `action` property describes an [action](./action) to take regarding a message,
and is a string containing one of the following **required** values:

* `create` - Used when creating a new resource.
* `read` - Used when requesting a resource. (Optionally uses the `have_version` property.)
* `update` - Used when updating an existing resource. (Requires the `resources` property on
	the `message` object.)
* `delete` - Used when deleting an existing resource. (Requires the `resources` property on
	the `message` object.)

### `have_version` (string, optional)

If the node already has a message, and is checking for updated versions, they **may**
include this property. The `have_version` property is the hexadecimal value of the
most recent version of the message that the node has.

### `resources` (collection of strings, optionally required)

The `resources` property is a YAML collection of strings which are the resource identifiers
of previous versions of a resource.

This property is **required** whenever the `action` property is `update` or `delete`.

### `control` (object, optional)

This object describes the addition and updating of users and nodes, the relationships
between users, and the trust level between user and node. See the [control specs](./control)
for more information.

### `response` (object, optionally required)

The `response` property is an object containing properties as described in
the [response specs](./response), it is **required** on **all** response messages.
