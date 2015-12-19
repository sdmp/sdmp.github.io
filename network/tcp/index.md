---
layout: docs
title: TCP
subtitle: Specifications for a synchronization implementation over TCP.
---


Implementing the protocol over TCP is done by establishing [session keys](../session/)
and afterward sending and receiving [synchronization messages](../synchronization/)
between nodes.

---

## Overview

After the TCP connection has been established, the node initiating the connection
sends a [connection object](../session/#connection-object). The receiving node
validates the signature and responds with its own connection object.

After this, the nodes send all data over the network as specified in the
[session specs](../session/#network-traffic).

---

## Connection Dropping

When dropping a connection during [connection validation](../session/#connection-validation),
the following process is followed in all cases:

1. The plaintext message `nope` is transmitted.
2. The connection is then dropped.

It is *not* permitted that a node transmit any other information concerning the cause of the drop.

---

## Network Communication

Each node may at any time transmit to the other node. All transmitted communications must be in
the format of a valid [SDMP container](../container/).

---

## Connection Overview

The process for establishing a secure connection is as follows:

1. The local node creates a TCP connection to the remote node.
2. The local node sends a connection object to the remote node.
3. The remote node verifies the connection object, dropping the connection if there is an error.
4. The remote node sends a connection object to the local node.
5. The local node verifies the connection object, dropping the connection if there is an error.
6. The local and remote node calculate the shared session key.

All messages after this point are transmitted using the calculated session key, according to
the *data transmission* algorithm.

---

## Data Transmission

The following ordered steps must be performed prior to transmitting any data across the network:

1. The data must be compressed using the [DEFLATE][deflate] algorithm specified in [RFC 1951][rfc1951].
2. The compressed data must be [zero-byte padded](../cryptography.) to the nearest **256 bytes**.
3. The compressed and padded data must be encrypted using the calculated session key.

Received data follows the reverse of those steps.


[deflate]: https://en.wikipedia.org/wiki/DEFLATE
[rfc1951]: https://www.ietf.org/rfc/rfc1951.txt
