---
version: 0.12
parent: network
layout: spec
title: handshake
subtitle: Establishing and using a session key for network communications.
---


The network connection handshake described below establishes a
session-unique key in a network-agnostic manner. This handshake
procedure should be supported by all implementations.

---

## Handshake Overview

In order to establish [forward secrecy][w_forward], the SDMP
uses the following [Diffie-Hellman][w_dh] key exchange
as the preferred way to establish a session key for a connection:

### Handshake: Send Session Creation Object

1. The node initiating the connection sends the receiving node a
  [session creation object](#session-creation).
2. If the receiving node accepts the session key, it will respond
  with its own session creation object. (If not, it will respond
  with an appropriate [response object](#session-response).)

### Handshake: Verify Signatures

Since both nodes have each others session creation object, each
node has enough information to complete the Diffie-Hellman shared
key calculation; the following objects must be sent as
[encrypted network objects](../../network/traffic), encrypted
using this calculated key.

1. The initiating node sends its session creation object inside
  a [signature](../../core/signature) object to the receiving node,
  inside an encrypted network object.
4. If the receiving node trusts the initiating node, it sends its session
  creation object to the initiating node, inside an encrypted network
  object. (If not, it will respond with an appropriate
  [response object](#session-response), also inside an encrypted
  network object.)

At this point each node has established the identity of the other node,
and has proof that the session key was established by the identified node.

Normal network communication can proceed using
[encrypted network objects](../../network/traffic), encrypted
using the shared key.

---

## Session Creation

The *session creation object* is a [container](../../core/container)
object where the **only** specified schema is the
[shared key](../../network/shared_key) schema, and **no**
other metadata is allowed.

Specifically, the [key fingerprint](../../core/cryptography#key-fingerprint)
or any other information potentially identifying the node initiating the
connection must **not** be exposed in this object.

The [UTF-8][w_utf8] encoded string representation of the JSON object
is sent as the data stream for session creation.

E.g., the data stream sent is the output of the function:

	UTF8_ENCODE(JSON.stringify(Session Creation Object))

---

## Session Response

If the node rejects the session creation object, or it does not
recognize the initiating node as a trusted node, it *should* send
a rejection response prior to dropping the connection. This rejection
response is a [container](../../core/container) object where the **only**
specified schema is the [response](../../journal/response) schema, and
**no** other metadata is allowed.

Specifically, the [key fingerprint](../../core/cryptography#key-fingerprint)
or any other information about the responding node must **not** be
exposed in this object.

---

## Session Signature

In order to establish the originator of the session key, after the
session connection objects are exchanged, they are re-sent inside
a [signature object](../../core/signature) signed by the originating node.

This information allows the receiving node to verify that the
sending node actually sent the session connection, removing the
possibility of man-in-the-middle attacks.

---

## No Additional Data

Placing *any* additional information or metadata in the
[session creation object](#session-creation) beyond what
is specified in the object schema is considered an error.

If this state is detected at any point, the network connection *must*
be rejected.

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

[w_dh]: https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange
[w_forward]: https://en.wikipedia.org/wiki/Forward_secrecy
[w_utf8]: https://en.wikipedia.org/wiki/UTF-8
