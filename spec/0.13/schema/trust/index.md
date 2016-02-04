---
version: 0.12
parent: schema
layout: spec
title: trust
subtitle: Create a trust between users and nodes.
---


Users and nodes can authorize other [identities](../../core/identity)
to take specific actions on their behalf. This is done by
publishing a resource which references the identity, and
lists the actions that identity can take.

---

## Trusts

Trusts are [resources](../../journal/resource) created by
[identities](../../core/identity), and list the actions
authorized by the trust.

The trust resource must be signed by the user key, or
by any node previously authorized by the user to create
new trusts.

---

## Trust Action

The following trust actions may be given to any trust:

### `read_resource`

When a node requests a resource published by another user,
that resource should only be given if the node or user has
been given the `read_resource` authorization by the user.

### `publish_resource`

When verifying published [resources](../../journal/resource) claiming
to originate from another user, only those resources signed by
keys authorized with `publish_resource` should be accepted as valid.

The `publish_resource` authorization does **not** include authorization
to create or revoke trusts. See later in this document for those
additional authorization types.

E.g. if user `A` authorizes node `B` with `publish_resource`,
a [user information](../user) resource signed
by node `B` should be considered equivalent in authority to those
signed directly by user `A`.

### `read_encrypted`

When sending [encrypted](../../core/encrypted) data to another
user, the [encryption key](../../core/encrypted#encryption) should
be encrypted to all public keys which have been given this
authorization by the recipient user.

E.g., if user `A` gives node `B` and `C` the `read_encrypted`
authorization, sending an encrypted [message](../message) to
user `A` would involve encrypting that message to all three
public keys.

### `create_trust`

This authorization allows the trusted key to create additional
trusts on behalf of the one signing the trust.

For a trust resource to be valid, the key signing the trust
must either be a [user identity](../../core/identity) key, or a
node key given the `create_trust` authorization.

### `revoke`

By default a node can [revoke resources](../revoke) it
publishes. This additional authorization allows the trusted
key to revoke resources published by the one signing the trust.

---

## Description

This object holds the information used to create a trust,
and to authorize the trustee to take specific actions on
behalf of the one signing the trust.

This object contains the following properties:

###### `trust` *(object)*

Holds the properties used to create the trust.

This contains the following reserved properties:

###### `trust.trustee` *(string, required)*

The [key fingerprint](../../core/cryptography#key-fingerprint) of the
user or node being given the trust.

###### `trust.authorization` *(array of strings, required)*

Lists the authorizations given to the trust. Must have at least one entry.

Each entry of this array must be one of the supported trust authorizations:

* `read_encrypted`
* `read_resource`
* `publish_resource`
* `create_trust`
* `revoke`

###### `trust.expires` *(string, optional)*

After this timestamp, the trust must not be considered valid. The timestamp
must be in UTC, formatted using [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601),
with granularity to the millisecond. E.g., `2015-07-26T15:48:37.703Z`.

An application should treat this trust as [revoked](../revoke)
after the date specified.

---

## Schema

	{
	  "$schema": "http://json-schema.org/draft-04/schema#",
	  "type": "object",
	  "properties": {
	    "create_trust": {
	      "type": "object",
	      "properties": {
	        "trustee": {
	          "type": "string"
	        },
	        "authorization": {
	          "type": "array",
	          "items": {
	            "enum": [
	              "read_encrypted",
	              "publish_resource",
	              "read_resource",
	              "create_trust",
	              "revoke_trust"
	            ]
	          }
	        },
	        "expires": {
	          "type": "string",
	          "pattern": "^\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{3}Z$"
	        }
	      },
	      "required": [ "trustee", "authorization" ]
	    }
	  },
	  "required": [ "create_trust" ]
	}
