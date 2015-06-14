---
layout: index
---


A protocol for distributing cryptographically secure messages across an encrypted
peer-to-peer network.

---

# Purpose

At it's heart the SDMP is just a way for machines to communicate messages to each
other in a very secure way that humans can understand.

If humans can understand it, they can debug it easier, and make sure the secure
part is actually secure.

---

# Documentation

* [Cryptography](/docs/cryptography): Cryptographic standards used in the protocol.
* [Resources](/docs/resources): Core protocol resources and how to interpret them.
* [Node Journal](/docs/journal): Each node maintains a journal of messages it publishes.
* [Communication](/docs/communication): How nodes publish journal updates, and request
	and respond with resources.
* [Session Keys](/docs/session): Establishing a session key for network communications.
* [TCP Network](/docs/tcp): Implementation of communication and session specifications over TCP.

---

## Get involved

* Read through this entire document and make sure it is human-readable, consistent, and sane.
* [File issues][issues] if you find anything wrong or questionable.
* Create [pull requests][pullrequest] if you want to propose a fix, including spelling
	or grammar errors!
* If you have questions, you can even [file issues][issues] for those, too!

---

## Release cycle

Updates to the specifications will follow the [Semantic Versioning 2.0.0][semver] approach:

* Major: Changes which are not backwards compatible.
* Minor: Changes which are fully backwards compatible.
* Patch: Changes which do not affect functionality.

Note also, from the semver specs:

> Major version zero (`0.y.z`) is for initial development. Anything may change at any
> time. The public API should not be considered stable.


[sdmprepo]: https://github.com/sdmp
[vol]: http://veryopenlicense.com/
[semver]: http://semver.org/
[issues]: https://github.com/sdmp/sdmp.github.io/issues
[pullrequest]: https://github.com/sdmp/sdmp.github.io/pulls
