---
version: 0.12
layout: spec
title: Network
subtitle: Establishing and using a session key for network communications.
---

Network traffic is sent in an [encrypted](/core/encrypted) object,
using a shared key established during the connection handshake.

## [Shared Key](./shared_key)

Establish a shared key for encrypting network traffic, using
the [Diffie-Helman][w_diffiehelman]

## [Connection Handshake](./handshake)

Create the shared key and verify the author.

## [Traffic](./traffic)

All network traffic uses the established shared key to
encrypt data in the form of [encrypted container](/core/encrypted).


[w_diffiehelman]: https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange
