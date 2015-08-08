---
layout: docs
title: Network
subtitle: Establishing a session key for network communications.
---


In order to establish perfect [forward secrecy][w_forward], the SDMP requires the
use of the [Diffie-Helman][w_diffiehelman] key exchange as the way to establish a
session key for a connection.

You can [read here](http://sdmp.github.io/papers/diffie-helman/) for a discussion
in greater detail of why the Diffie-Helman key exchange is used.

---

## Review: Diffie-Helman Key Exchange

The generalized [Diffie-Helman][w_diffiehelman] key exchange looks like:

1. Alice and Bob agree in advance to use the values `p` and `g`
2. Alice chooses secret `a` and sends Bob `A`, where `A=(g^a)%p`
3. Bob chooses secret `b` and sends Alice `B`, where `B=(g^b)%p`
4. Alice computes `s`, where `s=(B^a)%p`
5. Bob computes `s`, where `s=(A^b)%p`
6. Alice and Bob share the secret `s`, which is the session key

---

## Connection Object

Establishing a connection session key is done by sharing the necessary values between
nodes in order to calculate the secret `s` using the Diffie-Helman key exchange. This
is done by each node sending an [SDMP container](../container/) where the payload
uses **only** the [`connection` schema](#connection). This container object is
referred to as the *connection object*.

---

## Connection ([schema: `connection`][schema_connection])

Contains the values necessary to establish a [Diffie-Helman][w_diffiehelman] key exchange.
This object contains the following properties:

###### `connection` *(object, required)*

Used to hold the connection parameters.

###### `connection.g` *(integer, required)*

The value `g` from the Diffie-Helman key exchange.

###### `connection.p` *(string, required)*

The value `p` from the Diffie-Helman key exchange, whose octets are
[unpadded base64url](https://tools.ietf.org/html/rfc4648#section-5) encoded.

###### `connection.N` *(string, required)*

The value `N` from the Diffie-Helman key exchange (where `N=(g^n)%p`) whose octets are
[unpadded base64url](https://tools.ietf.org/html/rfc4648#section-5) encoded.

---

## No Additional Data

Placing *any* additional information or metadata in the connection object beyond what
is specified in the [container](../container/) specs and the [`connection` schema][schema_connection]
is considered an error. If this state is detected the connection *must* be rejected.

---

## Connection Validation

When a node receives a connection object, if the following circumstances are detected the
connection *must* be rejected:

* If the node cannot verify the signature in the object for any reason, including but not limited to:
	- malformed data,
	- an improperly generated signature,
	- the node does not have the public key of the node which generated the object.
* If the receiving node does not accept the values of `g`, `p`, or `N` for any reason.

---

## Connection dropping

When dropping or refusing a connection during connection object validation, it is *not*
permitted that the node transmit any information concerning the cause of the drop.

---

## Network traffic

After both nodes have exchanged connection containers, they have the necessary information to
calculate the shared session key. After this point, all connection network traffic must be
encrypted using this established key.

---

## Transmitting data

The following ordered steps must be performed prior to transmitting any data across the network:

1. The data must be compressed using the [DEFLATE][deflate] algorithm specified in [RFC 1951][rfc1951].
2. The compressed data must be [zero-byte padded](../cryptography.) to the nearest **256 bytes**.
3. The compressed and padded data must be encrypted using the calculated session key.

Received data follows the reverse of those steps.


[w_diffiehelman]: https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange
[w_forward]: https://en.wikipedia.org/wiki/Forward_secrecy
[deflate]: https://en.wikipedia.org/wiki/DEFLATE
[rfc1951]: https://www.ietf.org/rfc/rfc1951.txt
[schema_connection]: https://github.com/sdmp/sdmp-schema/blob/master/schemas/connection.json
