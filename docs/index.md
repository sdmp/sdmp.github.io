---
title: Documentation
subtitle: Specifications and implementation for the Secure Distributed Messaging Protocol (SDMP).
layout: index
---


If you are implementing the SDMP in some other language, or you want to understand
the technical side of it better, you'll probably want to start here.

## The full list

* [**Action**](./action) - Actions available to nodes.
* [**Connectio:**](./connect) - The connection/handshake process.
* [**Controls**](./control) - Document specifications for core functionality.
* [**Crypto**](./cryptography) - Cryptographic requirements for keys and hashes.
* [**Messages**](./message) - Definition of the basic data.
* [**Response**](./response) - How a node should respond.
* [**Signed YAML**](./signed-yaml) - Including a signature in a YAML file.
* [**Synchronization**](./sync) - How messages are synchronized between nodes.
* [**URI Scheme**](./uri-scheme) - Linking to other messages.

## The summary

* A "node" is just a computer allowing SDMP connections.
* All the messages passed between nodes are [YAML](yaml) documents.
* To verify authorship of a message, all messages are signed.
* Therefore all messages passed between nodes are actually [SYML](./signed-yaml) documents.
* Before two nodes can connect, they need to have each other's public key.
* (Distributing public keys is done using [specific SYML documents](./control).)
* When two nodes [connect](./connect) they do a Diffie-Helman key exchange.
* To establish the connecting node's identity, the handshake is encrypted.
* After connection, the node that started the connection publishes or requests [messages](./message).
* Node connect to each other periodically to [synchronize](./sync) data.

[yaml]: https://en.wikipedia.org/wiki/YAML
