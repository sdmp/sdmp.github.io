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

* [Introduction](/introduction): A less formal summarization of the protocol.
* [Core](/core): The core technology used by this protocol.
	- [Cryptography](/core/cryptography): Key sizes and algorithms used.
	- [Container](/core/container): Extendable, definable JSON object.
	- [Identity](/core/identity): Public keys used to identify users or nodes.
	- [Encrypted](/core/container): A container holding encrypted data.
	- [Signature](/core/signature): A container holding signed data.
* [Journal](/journal): Publishing resources to other users or nodes.
	- [Resource](/journal/resource): The thing published is a signature container.
	- [Structure](/journal/structure): Each node maintains its own journal.
	- [Broadcast](/journal/broadcast): How the resources get published.
	- [Request Journal](/journal/request_journal): Nodes request updated journal lists.
	- [Request Resource](/journal/request_resource): Nodes request individual resources.
	- [Response](/journal/response): Nodes respond with signature containers.
* [Schema](/schema): Other container extensions core to the protocol.
	- [Trust](/schema/trust): Users and nodes indicate trust chains.
	- [Revoke](/schema/revoke): Revoke any published resource.
	- [Node](/schema/node): Information about a node.
	- [User](/schema/user): Information about a user.
	- [Message](/schema/message): A [MultiMarkdown][multimark] formatted personal message.
	- [Receipt](/schema/receipt): Signal to a user or node when a private message
		or other resource is finally acquired.
* [Example](/example): Step by step example using command line tools.

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
[multimark]: https://en.wikipedia.org/wiki/MultiMarkdown
