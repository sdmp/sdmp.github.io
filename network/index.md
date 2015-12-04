---
layout: docs
title: Network
subtitle: Establishing and using a session key for network communications.
---


All network communcation should be encrypted in a way that respects
and establishes [forward secrecy][w_forward]. The network connection
handshake described below establishes a session-unique key in a
network-agnostic manner. This protocol should be supported by all
implementations.

---

## Handshake Overview

In order to establish [forward secrecy][w_forward], the SDMP
uses the following [Diffie-Helman][w_diffiehelman] key exchange
as the preferred way to establish a session key for a connection:

### Handshake: Send Session Creation Object

1. The node initiating the connection sends the receiving node a
	[session creation object](#session-creation).
2. If the receiving node accepts the session key, it will respond
	with its own session creation object. (If not, it will respond
	with an appropriate [response object](#session-response).)

### Handshake: Verify Signatures

Since both nodes have each others session creation object, each
node has enough information to complete the Diffie-Helman shared
key calculation; the following objects must be sent as
[encrypted network objects](#network-communication), encrypted
using this calculated key.

1. The initiating node sends its session creation object inside
	a signed [resource](/resource) object to the receiving node,
	inside an encrypted network object.
4. If the receiving node trusts the initiating node, it sends its session
	creation object to the initiating node, inside an encrypted network
	object. (If not, it will respond with an appropriate
	[response object](#session-response), also inside an encrypted
	network object.)

At this point each node has established the identity of the other node,
and has proof that the session key was established by the identified node.

Normal network communication can proceed using
[encrypted network objects](#network-communication), encrypted
using the shared key.

---

## Session Creation

The *session creation object* is a [container](/container) object where
the **only** specified schema is the [session_key](/schema/session_key)
schema, and **no** other metadata is allowed.

Specifically, the [key fingerprint](/cryptography#key-fingerprint)
or any other information about the node initiating the connection
must **not** be exposed in this object.

This JSON object is sent as the data stream for session creation.

---

## Session Response

If the node rejects the session creation object, or it does not
recognize the initiating node as a trusted node, it *should* send
a rejection response prior to dropping the connection. This rejection
response is a [container](/container) object where the **only**
specified schema is the [response](/schema/response) schema, and
**no** other metadata is allowed.

Specifically, the [key fingerprint](/cryptography#key-fingerprint)
or any other information about the responding node must **not** be
exposed in this object.

---

## Session Signature

In order to establish the originator of the session key, after the
session connection objects are exchanged, they are re-sent inside
a [resource object](/resource) signed by the originating node.

This information allows the receiving node to verify that the
sending node actually sent the session connection, removing the
possibility of man-in-the-middle attacks.

---

## Network Communication

An *encrypted network object* is a [container](/container) object where
the **only** specified schema is the [encrypted](/encrypted) schema,
and **no** other metadata is allowed.

Specifically, the [key fingerprint](/cryptography#key-fingerprint)
or any other information about the node initiating the connection
must **not** be exposed in this object.

This JSON object is sent as the data stream for all network traffic
after the session key is established.

---

## No Additional Data

Placing *any* additional information or metadata in the
[session creation object](#session-creation) or the
[encrypted network object](#network-communication)
beyond what is specified in their respective schemas
is considered an error. If this state is detected the
network connection *must* be rejected.

---

## Connection Validation

When a node receives a [session creation object](#session-creation), if
the following circumstances are detected the connection *must* be rejected:

* If the node cannot verify the signature in the object for any reason,
	including but not limited to:
	- malformed data,
	- an improperly generated signature,
	- the node does not have the public key of the node which
		generated the object.
* If the node does not accept the values of `g`, `p`, or `N` for any reason.

---

## Network traffic

After both nodes have [verified](#handshake-verify-signatures) the session
creation objects, they have the necessary information to calculate the
shared session key. After this point, all connection network traffic *must*
be encrypted using this established key.


[w_diffiehelman]: https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange
[w_forward]: https://en.wikipedia.org/wiki/Forward_secrecy
