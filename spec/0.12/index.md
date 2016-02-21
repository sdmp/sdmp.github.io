---
title: Version 0.12
subtitle: Development version release
layout: spec
---

## Release Notes

Please note that this release is still major version `0`, which [semver][semver]
says means:

> Major version zero (`0.y.z`) is for initial development. Anything may change
> at any time. The public API should not be considered stable.

Please see the [roadmap](/roadmap) for more detailed release plans, and the
[contributing guideline](https://github.com/sdmp/sdmp.github.io/blob/master/CONTRIBUTING.md)
for information on how to get involved.

---

## Table of Contents

* [Introduction](./introduction): A less formal summarization of the protocol.
* [Core](./core): The core technology used by this protocol.
	- [Cryptography](./core/cryptography): Key sizes and algorithms used.
	- [Container](./core/container): Extendable, definable JSON object.
	- [Identity](./core/identity): Public keys used to identify users or nodes.
	- [Encrypted](./core/encrypted): A container holding encrypted data.
	- [Signature](./core/signature): A container holding signed data.
* [Journal](./journal): Publishing resources to other users or nodes.
	- [Resource](./journal/resource): The thing published is a signature container.
	- [Structure](./journal/structure): Each node maintains its own journal.
	- [Broadcast](./journal/broadcast): How the resources get published.
	- [Request Journal](./journal/request_journal): Nodes request updated journal lists.
	- [Request Resource](./journal/request_resource): Nodes request individual resources.
	- [Response](./journal/response): Nodes respond with signature containers.
* [Schema](./schema): Other container extensions core to the protocol.
	- [Trust](./schema/trust): Users and nodes indicate trust chains.
	- [Revoke](./schema/revoke): Revoke any published resource.
	- [Node](./schema/node): Information about a node.
	- [User](./schema/user): Information about a user.
	- [Message](./schema/message): A [MultiMarkdown][multimark] formatted personal message.
	- [Receipt](./schema/receipt): Signal to a user or node when a private message
		or other resource is finally acquired.
* [Network](./network): Network agnostic connection details.
	- [Shared Key](./network/shared_key): Creating a shared, signed, symmetric key.
	- [Handshake](./network/handshake): Establishing a secure network connection.
	- [Traffic](./network/traffic): Rules for network traffic.
* [Examples](./example): Step by step examples, using command line tools.


[semver]: http://semver.org/
[multimark]: https://en.wikipedia.org/wiki/MultiMarkdown
