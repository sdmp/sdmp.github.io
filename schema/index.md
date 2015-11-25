---
layout: docs
title: Schema
subtitle: Schemas for container extensions which are part of the SDMP core.
---


Within the SDMP, a [container](/container/) object is "extended"
by specifying a [JSON Schema](http://json-schema.org/) within the
object (see [here](/container/#container-extension) for more details).

Several schema are specified as part of the core SDMP implementation,
and these schema are listed here.

---

## [container](/container)

All the schemas listed on this page are extensions of this core schema,
which is a JSON object with a few defined properties.

---

## [resource](/resource)

Anything published within the SDMP must be a valid resource. This
schema is basically a signed, stringified JSON object.

---

## [encrypted](/encrypted)

Communication within the SDMP is usually done in an encrypted manner. This
schema defines that encryption method.

---

## [identity](/schema/identity)

Creating a user or node requires publishing an identity. This schema contains
the public key of the user or node being created.

---

## [information_user](/schema/information_user)

Information about the user is published separately from the user identity
resource. This schema contains information about the user, such as a name.

---

## [information_node](/schema/information_node)

Information about the node is published separately from the node identity
resource. This schema contains information about the node, such as an IP
address and port, or other protocol details necessary for connecting to
the node.

---

## [relationship](/schema/relationship)

Users establish chains of trust between users and users, or between
users and nodes. This schema is the cryptographic "proof" of that
trust chain or relationship.

---

## [message](/schema/message)

This schema defines simple human-readable messages, which are typically
passed between users encrypted.

---

## [message_receipt](/schema/message_receipt)

When sending a message across the network, this schema offers a secure
way to let the sender know the message was received.

---

## [request_resource](/schema/request_resource)

This schema is used by a node to request a specific resource from
another node.

---

## [resource_response](/schema/response)

When a node responds to a resource request, it does so using this schema.

---

## [journal](/schema/journal)

As part of [synchronization](/synchronization), a node publishing a
resource does so by updating its own [journal](/journal), and then
broadcasting this update to all trusted nodes. This schema defines
that broadcast.

---

## [request_journal](/schema/request_journal)

If a node is offline for some time, it will become de-synchronized from
the other trusted nodes. As part of [synchronization](/synchronization),
a node will periodically request [journal](/journal) entries from other
trusted nodes.

---

## [session_key](/schema/session_key)

In order to have [forward secrecy](https://en.wikipedia.org/wiki/Forward_secrecy),
each time two nodes connect they must establish a new key for that
session. This schema is used in the handshake process used to
establish these session keys.
