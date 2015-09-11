---
layout: index
---


A protocol for distributing cryptographically secure messages across an encrypted
peer-to-peer network.

---

# Purpose

At its heart the SDMP is just a way for machines to communicate messages to each
other in a secure way that humans can understand.

If humans can understand it, it's easier to make sure the secure part is actually secure.

---

## Get Involved

* Read through this entire document and make sure it is human-readable, consistent, and sane.
* [File issues][issues] if you find anything wrong or questionable and just want to let us know.
* Create [pull requests][pullrequest] if you want to propose a fix. Spelling and grammar
  corrections are welcome!
* If you have questions, you can even [file issues][issues] for those, too!

---

## Release Cycle

Updates to the specifications will follow the [Semantic Versioning 2.0.0][semver] approach:

* Major: Changes which are not backwards compatible.
* Minor: Changes which are fully backwards compatible.
* Patch: Changes which do not affect functionality.

Note from the semver specs:

> Major version zero (`0.y.z`) is for initial development. Anything may change at any
> time. The public API should not be considered stable.

---

# Table of Contents

* [Overview](#overview)
	- [What are nodes and users?](#nodes-and-users)
	- [Sending and receiving messages](#messaging)
	- [Users have a "master" key pair](#user-key-pair)
	- [A user authorizes a node to publish or host](#node-authorization)
	- [Nodes connect to other nodes](#node-behaviour)
	- [A user sends messages to other users through nodes](#sending-messages)
	- [Users tell other users whether they know each other](#user-relationships)
	- [Nodes message other nodes if their users know each other](#node-to-node-trust)
* [Cryptography](#cryptography)
	- [Keys are basically just RSA](#rsa-keys)
	- [Signatures use those keys](#signature)
	- [Hashes are with SHA-512](#hashing)
	- [Encryption is with AES](#aes-encryption)
	- [Padding uses null-bytes](#padding)
* Network Topography
	- Nodes connect to other nodes
	- They periodically send out journal updates
	- They can request journal updates
	- And they can request specific resources
* Relationships
	- Users to other users
	- Users to nodes
	- Nodes to other nodes
* Container Object
	- Most things use the container object
	- The container object is a defined JSON object
	- You can specify other schemas for the container
	- The SDMP specifies a number of core schemas:
		- connection: establish a session key for communication
		- container: the core JSON object
		- encrypted: basically just a JWE object
		- identity: creates a user or node
		- information_node: describes a node
		- information_user: describes a user
		- journal: the resources that a node knows
		- message: private messages between users
		- receipt: let a user know you received a message
		- relationship: describe relationships between users and nodes
		- request_journal: request a journal update
		- request_resource: request a specific resource
		- resource: published to the journal
		- response: response to a request
* Resources
	- These are basically just containers that are "published"
	- To be published it must be signed
	- Signed with the user or node key
	- The container is basically just a JWS object
	- The JWS object goes inside a container object
	- That container object is the "resource"
	- You can link to a resource with a URI
* The Journal
	- The node keeps a list of resources it knows about
	- The node keeps that list synchronized with other nodes
* Sending Messages
	- You write to your own journal
	- Then you broadcast that journal update to everyone listening
	- The listening node can request the message at some point in the future
* Requesting Things
	- You ask a node for a resource or journal list
	- They respond with the data or some error
	- Requests and responses are linked with an identifier
* Session Keys
	- When to create and use a session key
	- The Diffie-Helman is used for creating session keys
	- The session key is signed with the known node key
* TCP Implementation
	- Connect using session keys
	- Traffic uses the session key
	- Packet size is padded to 16kib
	- Between nodes it's basically a base64 encoded JWE object
	- But when decrypted it's a JWS object
	- And the payload of the JWS object is a container

---

# Overview

A user runs an application (a "node") on their computer, and sends a message
to another user by publishing a "resource" to all trusted users.

It is anticipated that identities will initially be exchanged "out of band"
of the network. For example, keys might be exchanged over email and verified
in person. As the popularity of the protocol increases, other techniques are
likely to be available for establishing identity.

## Nodes and Users

A "user" is the human or organization who "owns" an account, while a "node" is
an application that is run on some hardware. For example, I am the "user", and
my laptop and smartphone each run a seperate "node".

## Messaging

The user creates a resource and "publishes" it with a node. Publishing a resource
adds an entry to the nodes journal list, and the node synchronizes this list to
all trusted nodes. Each node is free to request the resource from any other node.

This distribution mechanism is essentially the subscribe/publish pattern.

## User Key Pair

The identity of a user starts with a single [key pair](#rsa-keys). The public
section of this key pair is placed into an [identity resource](#TODO) and
made available to other users.

Although an identity resource is not automatically placed on a publically
accessible server, it should be considered "public" in the sense that a
key is made "public" by being published on the [MIT key server][mit_key_server].

Because nodes and users can explore the relationships of known nodes, the
identity resource for another user can theoretically be found "in the wild".

## Node Authorization

A user authorizes a node to publish content on the users behalf, or the user
authorizes a node to host other content on the users behalf.

For example, I would authorize the node application installed on my laptop
to publish content, thus allowing me to not need my master key pair to be
installed on the laptop, which is a security concern.

Alternately, I could authorize a node application installed on a remote
server to only host content, which would be encrypted in such a way that
the host node could not read the content. This would allow an always-on
server to speed up synchronization, much like an IMAP server.

## Node Behaviour

Nodes communicate by connecting directly to other trusted nodes and
transmitting content over an encrypted network.

For example, if my laptop runs a node application on my home network, and
my friend is running a trusted node on a laptop at a coffee shop, we would
need to be able to tunnel through the network (using port forwarding or
similar techniques) to connect directly to each other.

## Sending Messages

A user sends messages to other users by informing the recipient node *and*
other trusted nodes that new content is available. Upon receiving information
from a node that new content is available, the recipient node decides whether
to request that content or disregard it.

This mechanism is called [synchronization](#TODO) and uses the [journal list](#TODO).
It allows the propagation of content to be distributed across multiple nodes,
instead of the publishing node needing to upload a copy multiple times.

## User Relationships

Users tell other users whether they know each other by publishing a
[relationship resource](#TODO) that informs other users and nodes of
that trust. This resource is synchronized out to the trusted users
and nodes of the user publishing the resource.

## Node To Node Trust

A node cannot establish a network connection unless it has established a
trust with the connecting node. This trust is established in this way:

* If user A authorizes node B, and
* user X authorizes node Y, and
* user A and X have both published a relationship resource indicating
	that they know each other,

then nodes B and Y can trust each other.




 This trust is established by the meeting
the following criteria:

* The two nodes have the [identity resource](#TODO) of each other, and
* Each node has been authorized by a user, and
* The users who authorized the nodes have published

Two nodes trust each other if the users who authorized the nodes have
published the resource that indicates that the two users know each other.
This trust level is what allows the two nodes to connect to each other.


enough to allow a network connection



Since the authorization for nodes to publish or host content for a user is
synchronized to other nodes, two nodes connect if 

Nodes message other nodes if their users know each other

---

# Cryptography

Industry-standard cryptographic techniques are used to keep the network traffic
secure, to encrypt messages, and to verify users and nodes identities.

## RSA Keys

Where the phrase `public key`, `private key`, `key pair`, or `RSA key pair` is used, it
is meant that section or set of an asymmetric [RSA][w_rsa] key pair.

The length of the RSA key must be at least 2048 bits.

## Signature

Where the phrase `signature` is used, it is meant the string representation of the
RSA signature of the hash of the document, using the private key of the kay pair
specified.

## Hashing

Where the phrase `hash` or `digest` is used, it is meant the [SHA-512][w_sha2]
hashing algorithm as specified in [FIPS PUB 180-2][fips180].

Where a string representation of a hash is used or specified, it is meant the output of
the SHA-512 hashing algorithm, whose octets are [unpadded base64url][base64] encoded.

## Key Hash

Where the phrase `key hash` is used, it is meant the hash of the raw component of the
public section of the RSA key pair.

## Key Fingerprint

Where the phrase `key fingerprint` is used, it is meant the key hash of the public key.

## AES Encryption

Where the phrase `session key` or `token key` is used, it is meant an [AES][w_aes]
compatible key.

The bit length of the session key must be at least 256 bits.

## Padding

When padding is specified, it is meant that the data must be padded with null bytes.
This is also often referred to as zero-byte padding.

That is, the hexadecimal encoded value of the bytes is simply `00`.

---














---

# Relationships
## Users to other users
## Users to nodes
## Nodes to other nodes

---

# Container Object
## Most things use the container object
## The container object is a defined JSON object
## You can specify other schemas for the container
## The SDMP specifies a number of core schemas:
### connection: establish a session key for communication
### container: the core JSON object
### encrypted: basically just a JWE object
### identity: creates a user or node
### information_node: describes a node
### information_user: describes a user
### journal: the resources that a node knows
### message: private messages between users
### receipt: let a user know you received a message
### relationship: describe relationships between users and nodes
### request_journal: request a journal update
### request_resource: request a specific resource
### resource: published to the journal
### response: response to a request

---

# Resources
## These are basically just containers that are "published"
## To be published it must be signed
## Signed with the user or node key
## The container is basically just a JWS object
## The JWS object goes inside a container object
## That container object is the "resource"
## You can link to a resource with a URI

---

# The Journal
## The node keeps a list of resources it knows about
## The node keeps that list synchronized with other nodes

---

# Sending Messages
## You write to your own journal
## Then you broadcast that journal update to everyone listening
## The listening node can request the message at some point in the future

---

# Requesting Things
## You ask a node for a resource or journal list
## They respond with the data or some error
## Requests and responses are linked with an identifier

---

# Session Keys
## When to create and use a session key
## The Diffie-Helman is used for creating session keys
## The session key is signed with the known node key

---

# TCP Implementation
## Connect using session keys
## Traffic uses the session key
## Packet size is padded to 16kib
## Between nodes it's basically a base64 encoded JWE object
## But when decrypted it's a JWS object
## And the payload of the JWS object is a container

---



[sdmprepo]: https://github.com/sdmp
[issues]: https://github.com/sdmp/sdmp.github.io/issues
[pullrequest]: https://github.com/sdmp/sdmp.github.io/pulls
[vol]: http://veryopenlicense.com/
[semver]: http://semver.org/
[mit_key_server]: https://pgp.mit.edu/
[fips180]: http://csrc.nist.gov/publications/fips/fips180-2/fips180-2.pdf
[base64]: https://tools.ietf.org/html/rfc4648#section-5

[w_rsa]: https://en.wikipedia.org/wiki/RSA_(cryptosystem)
[w_sha2]: https://en.wikipedia.org/wiki/SHA-2
[w_aes]: https://en.wikipedia.org/wiki/Advanced_Encryption_Standard
