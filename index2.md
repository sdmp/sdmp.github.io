---
layout: index
version: 0.3
---


A protocol for distributing cryptographically secure messages across an encrypted
peer-to-peer network.

# Table of Contents

* [Purpose](#purpose) - Purpose and goal of the protocol
* [Related](#related) - Existing implementations, software using the protocol, and other related resources
* [License](#license) - Software license is basically public domain
* [Overview](#overview) - General overview and terminology
* [Cryptography](#cryptography) - The foundation of the SDMP is strong cryptography
* [Resources](#resources) - Resources available within the SDMP
* [Controls](#controls) - Creating users and nodes, establishing trust levels and connections
* [Documents](#documents) - Public or encrypted resources, typically with human-readable content
* [Network](#network) - Establishing a network connection between peers
* [Request/Response](#request-response) - Making and responding to network requests
* [Publish](#publish) - Publishing and synchronizing messages between peers

---

## Purpose

At it's heart the SDMP is just a way for machines to communicate messages to each
other in a very secure way that humans can understand.

If humans can understand it, they can debug it easier, and make sure the secure
part is actually secure.

---

# Related

* The SDMP makes use of the [SYML][syml] file type.
* You can use the SDMP from the [command line][sdmpcli].
* There's a JavaScript [module][sdmpnpm] you can use in your apps, or for reference.
* Check out the [relayreader][relayreader] project, which uses the SDMP.

---

## License

All code, documentation, and assets available under the entire [SDMP repo](sdmprepo) (including
this document) are published and released under the [Very Open License](vol).

---

## Overview

There are two identities: the *user* and the *node*.

* The *user* is the actual person or organization, and has one master public/private key.
* The *node* is the application used to create messages, and has a separate public/private key.

There are two relationships:

* A user authorizes a *node* to publish messages. This means a node operating on a laptop does
	not need to have the public/private key of the user accessible on that laptop.
* A *connection* is between a user and another user. Users indicate connections in order to
	allow synchronization of messages.

Publishing messages:

When a user [publishes](#publish) a message, that message is sent to the intended recipient, and
to all the nodes of the *user connections* of the user publishing the message. This is done so that
when the recipient is offline, they can still retrieve the message once they are back online.

Network connection:

Before a network connection is established, each node must *already* have the public key of the
other. This simplifies the connection process greatly, and allows a normal [Diffie-Helman](w_diffiehelman)
handshake, with the packets encrypted to the node's key.

---

## Cryptography

The SDMP uses industry-standard cryptographic techniques to keep the network
traffic secure, to strongly encrypt messages, and to verify a users identity.

### RSA public/private keys

Where the phrase `public key`, `private key`, or `key pair` is used, it is meant
an asymmetric [RSA][w_rsa] key pair.

The bit length of the RSA key **must** be at least 2048 bits.

### Hashing

Where the phrase `hash` or `digest` is used, it is meant the [`SHA-256`][w_sha2] hashing
algorithm as specified in [FIPS PUB 180-2](fips180), encoded to lower-case hexadecimal.

### Key hashing

Where a *key hash* is specified, it is meant the *PGP fingerprint*.

### PGP fingerprint

Where a PGP fingerprint is used, it is the complete V4 fingerprint as specified in
[RFC 4880](https://tools.ietf.org/html/rfc4880#section-12.2), encoded to lower-case hexadecimal.

E.g., `cd92815bf6273acbaf834b9faed277c722068291`.

### AES encryption

Where the phrase "session key" is used, it is meant an [AES][w_aes] compatible key of **at least** 256 bits.

---

## Resources

Resources passed between nodes are signed YAML ([SYML][syml]) streams.

### Resource types

There are only two types of resources published within the SDMP: [controls](#controls)
and [documents](#documents). Both of these are defined YAML/SYML streams.

Control resources contain publically visible information about nodes, users, and relationships
between the two.

Document resources are the general message type, and may contain public data, or data that
is visible only to the intended recipient.

### YAML restrictions

All resources **must** be valid [SYML][syml] streams, with the following additional
**required** restrictions:

* Multi-document YAML streams are **not** allowed.
* Comments inside the YAML stream are **not** allowed.
* More precisely, **only** values which are actual YAML properties or values are allowed.
* A single property at the root of the YAML document named `resource` is **required** and **reserved**.

### Properties of the `resource` object

The following properties of the `resource` object are reserved for use within the SDMP:

* `user` *[string, required]* The node sending a resource **must** include the key fingerprint
	of the user that has authorized the node to create the resource.
* `action` *[string, required]* Describes an action to take regarding a resource. **Must** be one
	of the following values: `create`, `read`, `update`, or `delete`.
* `versions` *[string collection, optionally required]* This property is **required** when
	the `action` property is `update` or `delete`, and is a YAML collection of resource
	identifiers of previous versions of the resource.
* `control` *[object, optional]* Describes the addition and updating of users and nodes,
	the relationships between users, and the trust level between user and node. This property
	is **required** for all [control resources](#controls).
* `document` *[object, optional]* This property is **required** for all non-control resources,
	and is an object containing properties defined in the [document specifications](#documents).

### Resource identifier

The hash of the YAML file which is signed to generate the SYML signature is the *resource identifier*.

### URI: Uniform Resource Identifier

Linking between messages is possible using the [URI][w_uri], which is constructed as follows:

	sdmp://<AUTHOR>[/<RESOURCE>]

* The URI scheme name is `sdmp`.
* `AUTHOR` is the key hash of the public key of the user who authored the resource.
* `RESOURCE` is **optional** and is the resource identifier.

URIs have the two possible forms:

* `sdmp://\<AUTHOR>/` - Requests of this type should yield the "create user" [control resource](#controls)
	of the user whose key fingerprint is `AUTHOR`.
* `sdmp://\<AUTHOR>/\<RESOURCE>` - Requests of this type should yield the resource whose
	resource identifier is `RESOURCE` and which has been published by the user
	whose key fingerprint is `AUTHOR`.

---

## Controls

Creating users and connections, creating nodes, and establishing trust levels between
nodes is done by publishing a "control" resource.

All control resources require the root `resource` object to have a property
named `control`, which is an object defining the type of control.

The following control types are defined:

### Create a node

The `control` object has the following **required** properties:

* `type` : The exact string: `node`
* `key` : The binary encoded public key of the node, which is equivalent to
	an unarmored export of an OpenPGP public key.

This resource SYML **must** be signed by the node.

Deleting this resource indicates that this node is no longer secure. Properly functioning
nodes will **not** communicate with a node once this resource is deleted.

Updating this resource is **not** allowed.

### Node information

The `control` object has the following **required** properties:

* `type` : The exact string: `node-info`
* `network` : A YAML string collection of the network addresses for the node.
* `name` : A human-friendly name for the node.

This resource SYML **must** be signed by the node.

The network addresses **must** be either IPv4 **or** IPv6.

Nodes **should** favor the IPv6 address, where possible.

Network addresses **must** include the port number.

### Node permission levels

Used to indicate a users level of trust in a node. This is what authorizes
a node to publish resources on a users behalf, or synchronize resources that
are intended for a user.

The `control` object has the following **required** properties:

* `type` : The exact string: `node-permission`
* `node` : The key fingerprint of the public key of the node.
* `user` : The key fingerprint of the public key of the user.
* `authority` : Currently only `host` and `publisher` are supported.
	- A node given `host` permissions by a user is authorized to
		synchronize resources for a user.
	- A node given `publisher` permissions by a user is authorized to
		create resources on the users behalf (i.e., an application on
		the users laptop).

This resource **must** be signed by the user.

Deleting this resource indicates that the user no longer trusts that node,
therefore resources from this node should **not** be synchronized.

### Creating a user

The `control` object has the following **required** properties:

* `type` : The exact string: `user`
* `key` : The binary encoded public key of the user, which is equivalent to
	an unarmored export of an OpenPGP public key.

This resource SYML **must** be signed by the created user.

Deleting this resource indicates that the user wishes to delete
*all* resources published by that user.

### User information

The `control` object has the following **required** properties:

* `type` : The exact string: `user-info`
* `info` : Any publicly visible information about the user. **Requires**
	at least the following fields:
	- `name` : The publicly visible name of the user.
	- `about` : A brief description of the user.

This resource **must** be signed by the user **or** by any node
authorized by the user as a `publisher`.

### Create connection

This resource is an indication of the users desire to subscribe to updates from
the connection, and the users willingness to publish updates to that connection.
See the [publication specifications](#publish) for additional details.

The `control` object has the following **required** properties:

* `type` : The exact string: `connection`
* `user` : The key fingerprint of the user being connected.
* `connection`: A YAML string collection containing at least one of the following strings:
	- `publish` : Indicates that messages from the user publishing the resource **may**
		be synchronized to the connection.
	- `subscribe` : Indicates that messages published by the connection **may** be
		synchronized to the user.

This resource **must** be signed by the user **or** by any node authorized by the
user as a `publisher`.

Deleting this resource indicates that the user no longer wishes to synchronize
resources to or from that connection.

---

## Documents

Resources published to the SDMP which are not control resources are called "documents".

All document resources require the root `resource` object to have a property
named `document`, which is an object containing minimal metadata about the resource.

There are two types of resources: *public* and *encrypted/mixed*.

### Encrypted/mixed documents

Documents containing encrypted data have the property `encrypted` on the `document`
object, which is the binary encoded encrypted message, equivalent to PGP unarmored
encryption output.

Because there is additional information available in the resource, it would be
misleading to indicate that the resource itself was encrypted, and a properly
functioning node application will **not** allow additional private metadata to
be inserted into the resource.

**Note:** Although the focus of the SDMP is not on anonymity, a properly functioning
node application will **not** make visible the recipients of any private message.
Exposing the recipients of a message makes the privacy of the users needlessly
difficult to protect.

### Public documents

Resources which contain additional metadata or content which is not encrypted are
considered *public* documents.

To further indicate that a resource is public, the boolean property `public` **must**
be set on the `document` object, and it **must** be set to `true`.

### Etiquette for public vs private

Publishing a resource containing a public document should be interpreted in much
the same way as publishing a public blog post. The information in the resource
should be considered available for general public viewing.

Publishing a resource which has not been marked as public is much like sending
an email: the recipient of the document must determine by context and discretion
whether a message should be relayed in whole, in part, or not at all.

Applications should make the distinction of public vs private clear to the
user, primarily so that a user does not accidentally send a private message
marked as public.

---

## Network

All network connections are between nodes, and secured using the [Diffie-Helman](w_diffiehelman)
key creation approach, with some minor modifications to authenticate the connecting node.

### Nodes must have keys

Before a network connection is established, each node **must** already have the public key
of the other.

### Connection is per user-authorization

Although there is nothing preventing a node from being authorized by multiple users, a
connection between nodes is established for a single user only.

This means a node cannot publish messages for multiple users under the same connection.

### Session key

A symmetric connection-unique AES key is created for the session.

Although there is no cryptographic function preventing key reuse, a properly functioning
node will **not** reuse session keys. Session keys **must** be unique to the session.

### Typical Diffie-Helman

A review of the general [Diffie-Helman](w_diffiehelman) key exchange:

1. Alice and Bob agree to use `p` and `g`
2. Alice chooses secret `a` and sends Bob `A`, where `A=(g^a)%p`
3. Bob chooses secret `b` and sends Alice `B`, where `B=(g^b)%p`
4. Alice computes `s`, where `s=(B^a)%p`
5. Bob computes `s`, where `s=(A^b)%p`
6. Alice and Bob share the secret `s`, which is the session key

### "Wrapped" Diffie-Helman

In order to avoid a man-in-the-middle attack, the values used in the Diffie-Helman exchange are
signed by the node initializing the connection, and are also encrypted to the receiving node.

This method requires that each node have previously obtained and verified the others' public key.

1. Alice has previously acquired and verified Bob's public key `bp`
2. Bob has previously acquired and verified Alice's public key `ap`
3. Alice and Bob agree to use `p` and `g`
4. Alice creates an encrypted message for Bob
	1. Alice chooses secret `a` and calculates `A`, where `A=(g^a)%p`
	2. Alice signs `A` as `S_A` and encrypts `A,S_A` to Bob's public key as `{A,S_A}_bp`
	3. Alice sends Bob the encrypted message `{A,S_A}_bp`
5. Bob creates an encrypted message for Alice
	1. Bob chooses secret `b` and calculates `B`, where `B=(g^b)%p`
	2. Bob signs `B` as `S_B` and encrypts `B,S_A` to Alices's public key as `{B,S_B}_ap`
	3. Bob sends Alice the encrypted message `{B,S_B}_ap`
6. Alice decrypts `{B,S_B}_ap`, verifies `S_B`, and then computes `s`, where `s=(B^a)%p`
7. Bob decrypts `{A,S_A}_bp`, verifies `S_A`, and then computes `s`, where `s=(A^b)%p`
8. Alice and Bob share the secret `s`, which is the session key

### Connection package

The node chooses a secret `n` and calculates `N` where `N=(g^n)%p`.

The values of `g`, `p`, and `N`, along with the [key fingerprint](./cryptography) of
the node public key, and the key fingerprint of the user the node represents, are
placed inside a YAML stream constructed according to the following rules:

* The YAML stream contains a single document.
* At the root of the YAML stream is a single object named `connection`.
* The **required** `connection` object properties are: `g`, `p`, `N`, `node`, and `user`.
* The values of `g` and `p` are integer-encoded.
* The value of `N` is `binary` encoded, according to [YAML specs](http://yaml.org/type/binary.html).
* The value of `node` is the key fingerprint string of the node initializing the connection.
* The value of `user` is the key fingerprint of the user which the node is representing.

This YAML stream is transformed into a [SYML](./signed-yaml) file, being signed using
the key identified by the `node` key fingerprint.

Before being sent over the network, the SYML file is encrypted to the public key of the
recipient node, using the following rules:

* The SYML stream is zero-byte padded to the nearest `4096` byte block.
* This stream is encrypted using the public key of the intended recipient node.

This encrypted stream is the connection package.

### Connection package validation

When a node receives a connection package, it drops the connection under the
following circumstances:

* If the connection package cannot be decrypted by the node.
* If the node cannot verify the signature in the SYML for **any** reason, including but not limited to:
	- malformed data,
	- an improperly generated signature, or
	- the node does not have the public key of the node identified by the fingerprint.
* If the node cannot verify that the connecting node has been authorized
	by the user identified by the `user` value
* If the node does not accept the values of `g`, `p`, or `N` for any reason.

### Connection dropping

When dropping a connection during connection package validation, the following process
is followed in **all** cases:

1. The plaintext message `nope` is transmitted.
2. The connection is then dropped.

It is **not** permitted that a node transmit **any** other information concerning
the cause of the drop.

### Connection procedure

The process for establishing a secure connection is as follows:

1. The local node creates a TCP connection to the remote node.
2. The local node sends a connection package to the remote node.
3. The remote node verifies the connection package, dropping the connection if there is an error.
4. The remote node sends a connection package to the local node.
5. The local node verifies the connection package, dropping the connection if there is an error.
6. The local and remote node calculate the shared session key.

All messages past this point **must** be encrypted to this session key.

---

## Request/Response

Request:
	- resource
	- message stack
Response:
	- resource not found
	- the actual resource
	- message stack unchanged
	- additional elements on the message stack
Publish:
	- resource
	- message stack
Response:
	- resource received
	- message stack received

---






## Publish

The primary difficulty of publishing messages across a decentralized network is that sending
messages must account for nodes being offline. Since the SDMP is primarily between personal
computers instead of dedicated network servers, this difficulty is the primary concern, and
is addressed with the publishing/synchronization scheme described here.

If the synchronization model is inefficient, it is because it errs on the side of simplicity,
so that the average software developer can understand it clearly.

### Sending messages is done to many nodes

Because nodes cannot be considered to be always-online, sending messages to a user involves
pushing messages to the nodes authorized as *host* by the user, **and** all nodes of the
users' connections.

### Node message stack

Each node maintains an ordered list of the *resource identities* of all messages it publishes,
as well as all messages published by any node authorized by a connection.

This stack is ordered simply by time of knowledge, instead of time of message publication. If
a node receives a published message several days after the publication, the resource identifier
for that message is simply added to the stack.

### Publishing a message

When the node publishes a message, it is added to the message stack of that node. The node then
attempts to connect to every node authorized as a host by the users connections, and synchronizes
the message stack.

### Synchronizing message stacks

When two nodes connect to each other, they synchronize the two message stacks.

This is done by each node exchanging the last known stack version of the other.

When the node gets a stack version, it responds with one of the following:

* The stack version is unknown
* The stack version is still the latest version.
* The stack version is now outdated.

For the last response, the response includes a list of the next resource identifiers on its own
message stack, such that the node receiving the response can grab the list and calculate the
latest version.

If the list is too large, the responding node may truncate it. If this is done, the node receiving
the list would have only a partial list. This would mean that the version of their next request
would also be outdated, and so the responding node would give additional elements of the list.
In this way, the responding node can "paginate" the list if it is too large.

When a node connects to another node, as part of the request it should include its latest version,
and since it should have knowledge of when the responding node was last contacted, it should be
able to construct a list of resource identifiers making up its own message stack. In this way the
connecting node can publish a new message: it connects and says "my latest version is X, last time
I connected I gave you my latest version Y, so here is my list of resource identifiers between
then and now".

### Message stack version

The "version" is the hash of the previous version, plus the hash of the new message, hashed together.

To calculate a version for `n`, you need to calculate `n` hashes:

	v_1 = hash(hash(m_1))
	v_2 = hash(v_1,hash(m_2))
	v_3 = hash(v_2,hash(m_3))
	...
	v_(n-1) = hash(v_(n-2),hash(m_(n-1)))
	v_n = hash(v_(n-1),hash(m_n))

### Sending the message stack

When a node sends a message stack, it is a list of the resource author (the user) and the resource
identifier:

	stack:
		7a51...79ee: cc0f...e187
		60ba...615f: 9260...3372
		ae3f...e302: 8600...98a0
		19e1...5927: 551f...ee3b

The node receiving the message stack can then determine if it cares about getting the message, and
also verify the latest version information.















---

## Get involved

* Read through this entire document and make sure it is readable, consistent, and sane.
* [File issues][sdmpissues] if you find anything wrong or questionable, anywhere on this page.
* Create [pull requests][sdmppullrequest] if you spot anything wrong or missing and you want to
	propose a fix, including spelling or grammar errors! (Please make pull requests against
	the `development` branch.)

---

## License

All documentation produced here is published and released under the [Very Open License](vol).


[sdmpcli]: https://github.com/sdmp/sdmp-cli
[sdmpnpm]: https://github.com/sdmp/sdmp-npm

[relayreader]: http://relayreader.com
[sdmpissues]: https://github.com/sdmp/sdmp.github.io/issues
[sdmppullrequest]: https://github.com/sdmp/sdmp.github.io/pulls
[sdmprepo]: https://github.com/sdmp

[syml]: http://sdmp.github.io/signed-yaml
[vol]: http://veryopenlicense.com/
[fips180]: http://csrc.nist.gov/publications/fips/fips180-2/fips180-2.pdf

[yaml]: https://en.wikipedia.org/wiki/YAML
[pgp]: https://en.wikipedia.org/wiki/Pretty_Good_Privacy
[w_diffiehelman]: https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange
[worm]: https://en.wikipedia.org/wiki/Write_once_read_many
[crud]: https://en.wikipedia.org/wiki/Create,_read,_update_and_delete
[bittorrent]: https://en.wikipedia.org/wiki/BitTorrent
[w_rsa]: https://en.wikipedia.org/wiki/RSA_(cryptosystem)
[w_sha2]: https://en.wikipedia.org/wiki/SHA-2
[w_aes]: https://en.wikipedia.org/wiki/Advanced_Encryption_Standard
[w_uri]: https://en.wikipedia.org/wiki/Uniform_resource_identifier
[w_httpcodes]: https://en.wikipedia.org/wiki/List_of_HTTP_status_codes
