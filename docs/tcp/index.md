---
layout: docs
title: TCP
subtitle: Specifications for a synchronization implementation over TCP.
---


Implementing the protocol over TCP is done by establishing [session keys](../session)
and afterward sending and receiving [request/response messages](../communication)
between nodes.

## Overview

After the TCP connection has been established, the node initiating the connection
sends a [connection message](../session/#connection-message).

The receiving node validates the signature, and responds with its own connection message.

After this, the nodes send all data over the network [as specified](../session/#transmitting-data).

## Connection message validation

After any node receives a connection message, it immediately drops the connection under the
following circumstances:

* If the connection message cannot be decrypted by the receiving node.
* If the node cannot verify the signature of the [SYML][syml] for any reason, including
	but not limited to:
	- malformed data
	- an improperly generated signature
	- the node does not have the public key of the node which generated the message.
* If the node does not accept the values given in the [connection message](../session/#connection-message)
	for anyh reason.

## Connection dropping

When dropping a connection during connection message validation, the following process
is followed in all cases:

1. The plaintext message `nope` is transmitted.
2. The connection is then dropped.

It is not permitted that a node transmit any other information concerning the cause of the drop.

## Network traffic encryption

After both nodes have exchanged connection messages, all network traffic for this connection must
be encrypted using the established session key, [as specified](../session/#transmitting-data).

## Network communication

Each node may at any time transmit to the other node. All transmitted communications must be in
the format of a valid [communication document](../communication/#communication-document-format).

## Connection overview

The process for establishing a secure connection is as follows:

1. The local node creates a TCP connection to the remote node.
2. The local node sends a connection message to the remote node.
3. The remote node verifies the connection message, dropping the connection if there is an error.
4. The remote node sends a connection message to the local node.
5. The local node verifies the connection message, dropping the connection if there is an error.
6. The local and remote node calculate the shared session key.
7. All future messages are padded and then encrypted using the shared session key.

[syml]: http://github.com/sdmp/signed-yaml
[newissue]: https://github.com/sdmp/sdmp.github.io/issues/new
