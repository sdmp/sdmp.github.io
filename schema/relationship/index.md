---
layout: schema
title: relationship
subtitle: Indicates a trust level between nodes and users.
---


A trusted node is a node trusted by a trusted user.







Relationships between users and between nodes is at the heart of any key
sharing system. In the SDMP, relationships are established and updated
using the `relationship` schema, which indicates the user-to-user or
user-to-node relationship.

---

## Relationship Types

The SDMP core has three defined relationship types:

* **`connection` (user to user):** A user indicating that another user is known to them. This
	indicates to other nodes that resources between those users may be [synchronized](/synchronize/).
* **`publisher` (user to node):** A user indicating that the node may publish resources on
	behalf of the user. Typical use is that a user authorizes a node as a publisher when
	that node is running on a computer operated by that user, such as a laptop or smartphone.
* **`host` (user to node):** A user indicates that the node may send and receive resources
	for the user, but may *not* publish new resources. This is similar to resources being
	[synchronized](/synchronize/) between users.

---

## Schema: `relationship` ([JSON Schema](https://github.com/sdmp/sdmp-schema/blob/master/schemas/relationship.json))

This object contains the following properties:

###### `relationship` *(object)*

Object holding information about the relationship.

###### `relationship.type` *(string, required)*

Defines the type of relationship. Must be one of the following: `connection`, `publisher`, `host`.

###### `relationship.identity` *(string, required)*

The [key fingerprint](/cryptography/#key-fingerprint) of the user or node to
which the relationship is being created. (In summary, this is equivalent to
the SHA-512 hash of the public key, whose octets are
[unpadded base64url][base64] encoded.)


[base64]: https://tools.ietf.org/html/rfc4648#section-5
