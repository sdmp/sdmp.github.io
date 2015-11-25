---
layout: index
---


A protocol for distributing cryptographically secure messages across an encrypted
peer-to-peer network.

---

## Purpose

At its heart the SDMP is just a way for machines to communicate messages to each
other in a secure way that humans can understand.

If humans can understand it, it's easier to make sure the secure part is actually secure.

---

## Documentation

* [Cryptography](/cryptography): Cryptographic standards used in the protocol.
* [Container](/container): Containers used to publish resources and communicate between nodes.
* [Resource](/resource): Resources passed between nodes and how to interpret them.
* [Schema](/schema): Extensions of the container object which are core to this protocol.
* [Node Journal](/journal): Each node maintains a journal of resources.
* [Synchronization](/synchronization): How nodes publish, request, and respond with resources.
* [Network](/network): Handshake to establish a session key for network communications.

---

## Implementations

* [TCP Network](/tcp): Specifications for a synchronization implementation over TCP.

---

## Articles

A few articles which might be useful to you:

* [Introduction to SDMP](/article/introduction): An informal overview of the
	entire protocol. Starting here might be best if you're new to the project.
* [Diffie-Helman](/article/diffie-helman): Discussion on why the DH was used.
* [Journal Heartbeat](/article/journal-heartbeat): A less formal description of
	the synchronization process.

---

## Get involved

* Read through this entire document and make sure it is human-readable, consistent, and sane.
* [File issues][issues] if you find anything wrong or questionable and just want to let us know.
* Create [pull requests][pullrequest] if you want to propose a fix. Spelling and grammar
  corrections are welcome!
* If you have questions, you can even [file issues][issues] for those, too!

---

## Release cycle

Updates to the specifications will follow the [Semantic Versioning 2.0.0][semver] approach:

* Major: Changes which are not backwards compatible.
* Minor: Changes which are fully backwards compatible.
* Patch: Changes which do not affect functionality.

Note from the semver specs:

> Major version zero (`0.y.z`) is for initial development. Anything may change at any
> time. The public API should not be considered stable.


[sdmprepo]: https://github.com/sdmp
[vol]: http://veryopenlicense.com/
[semver]: http://semver.org/
[issues]: https://github.com/sdmp/sdmp.github.io/issues
[pullrequest]: https://github.com/sdmp/sdmp.github.io/pulls
