---
layout: index
version: 0.9
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

# Related

* The SDMP makes use of the [SYML][syml] file type.
* There's a JavaScript [module][sdmpnpm] you can use in your apps, or for reference.
* Check out the [relayreader][relayreader] project, which uses the SDMP.

---

## License

All code, documentation, and assets available under the entire [SDMP repo](sdmprepo) (including
this document) are published and released under the [Very Open License](vol).

---

# Table of Contents

* [Release Cycle](#release-cycle) - The SDMP uses the semver approach
* [Overview](#overview) - General overview and terminology
* [Message Format](#message-format) - Format of messages passed around the network
* [Cryptography](#cryptography) - The foundation of the SDMP is strong cryptography
* [Network](#network) - Establishing a network connection between peers
* [Request/Response](#requestresponse) - Making and responding to network requests
* [Resources](#resources) - Resources available within the SDMP
* [Document Resources](#document-resources) - Public or encrypted resources, typically with human-readable content
* [Control Resources](#control-resources) - Creating users and nodes, establishing trust levels and connections
* [Publish/Synchronize](#publishsynchronize) - Publishing and synchronizing messages between peers

---

## Release cycle

Updates to the specifications will follow the [Semantic Versioning 2.0.0](http://semver.org/) approach.
with only a major and minor number. The summary of that is: Minor number bumps are for non-breaking changes,
while major number bumps are not backwards compatible.

Updates for spelling and grammar which are completely backwards compatible will **not** bump the minor number.

The scope and purpose of the SDMP is very limited, therefore it is anticipated that version
number bumps will be very infrequent.

**Note:** While still in draft (e.g. `0.x`), spelling and naming changes to variables which render the
specifications backwards incompatible will **not** bump the major version.

---

## Get involved

* Read through this entire document and make sure it is human-readable, consistent, and sane.
* [File issues][sdmpissues] if you find anything wrong or questionable, anywhere on this page.
* Create [pull requests][sdmppullrequest] if you spot anything wrong or missing and you want to
	propose a fix, including spelling or grammar errors! (Please make pull requests against
	the `development` branch.)
* If you have questions, you can even [file issues][sdmpissues] for those, too!

---

## Overview

There are two identities: the *user* and the *node*.

* The *user* is the actual person or organization, and has one master public/private key.
* The *node* is the application used to create messages, and has its own public/private key.

There are two relationships:

* A user authorizes a *node* to publish messages. This means a node operating on a laptop does
	not need to have the public/private key of the user accessible on that laptop.
* A *connection* is between a user and another user. Users indicate connections in order to
	allow synchronization of messages.

### Publishing messages:

When a user [publishes](#publishsynchronize) a message, that message is sent to the nodes of the
recipient, *and* to the nodes of the *connections*. This is done so that when the recipient is
offline, they can still retrieve the message once they are back online.

### Network connection:

Before a network connection is established, each node must *already* have the public key of the
other. This allows a [Diffie-Helman][w_diffiehelman] handshake (self-signed with the node's key)
to establish an asymmetric key, which is used for that connection session.

---

## Message format

Most data passed across the network will be [YAML][w_yaml] streams.

Where the term `YAML message` is used, it is meant a valid YAML stream
with the following additional restrictions:

* Multi-document YAML streams are **not** allowed.
* The YAML stream must use the start and end boundary characters, `---` and `...`,
	respectively. (See the [YAML specs][yamlboundaries] for more information on
	the boundary characters.)
* Comments inside the YAML stream are **not** allowed.
* More precisely, **only** values which are actual YAML properties or values are allowed.

---

## Cryptography

The SDMP uses industry-standard cryptographic techniques to keep the network traffic
secure, to strongly encrypt messages, and to verify users and nodes identities.

### RSA public/private keys

Where the phrase `public key`, `private key`, or `key pair` is used, it is meant
that section or set of an asymmetric [RSA][w_rsa] key pair.

The bit length of the RSA key **must** be *at least* 2048 bits.

### Hashing

Where the phrase `hash` or `digest` is used, it is meant the [`SHA-256`][w_sha2] hashing
algorithm as specified in [FIPS PUB 180-2][fips180], encoded to lower-case hexadecimal.

### Key hashing

Where the phrase `key hash` is used, it is meant the *PGP fingerprint*.

### PGP fingerprint

Where a PGP fingerprint is used, it **must** be the complete V4 fingerprint as specified
in [RFC 4880](https://tools.ietf.org/html/rfc4880#section-12.2), encoded to lower-case
hexadecimal, with no spaces or semicolons.

For example: `cd92815bf6273acbaf834b9faed277c722068291`

### AES encryption

Where the phrase `session key` is used, it is meant an [AES][w_aes] compatible key.

The bit length of the session key **must** be *at least* 256 bits.

---

## Network

Network connections are made between nodes, and secured using a self-signed [Diffie-Helman][w_diffiehelman]
handshake to establish the session key.

### Nodes must have keys

Before a network connection is established, each node **must** already have the public key
of the other.

### Diffie-Helman handshake review

The generalized [Diffie-Helman][w_diffiehelman] key exchange looks like:

1. Alice and Bob agree to use `p` and `g`
2. Alice chooses secret `a` and sends Bob `A`, where `A=(g^a)%p`
3. Bob chooses secret `b` and sends Alice `B`, where `B=(g^b)%p`
4. Alice computes `s`, where `s=(B^a)%p`
5. Bob computes `s`, where `s=(A^b)%p`
6. Alice and Bob share the secret `s`, which is the session key

### Self-signed Diffie-Helman connection message

Each node makes a *connection message*, which is a YAML document containing the
information necessary for the Diffie-Helman exchange, signed by the node creating
the connection message.

The node chooses a secret `n` and calculates `N` where `N=(g^n)%p`.

The values of `g`, `p`, and `N`, along with the [key fingerprint](#cryptography) of
the node public key, and the key fingerprint of the user the node represents, are
placed inside a [YAML message](#message-format) with the following requirements:

* At the root of the YAML stream is a single object named `connection`.
* The **required** `connection` object properties are: `g`, `p`, and `N`.
* The value of `g` is integer-encoded.
* The values of `p` and `N` are `binary` encoded, according to [YAML specs](http://yaml.org/type/binary.html).

This resulting YAML message is signed using the key of the node creating the message,
and is transmitted in clear-text across the network.

Placing any additional information or metadata in the message which would identify the
node creating the message is considered an error, and if this state is detected the
connection **must** be dropped.

### Connection message validation

When a node receives a connection message, it drops the connection under the
following circumstances:

* If the connection message cannot be decrypted by the receiving node.
* If the node cannot verify the signature in the message for **any** reason, including but not limited to:
	- malformed data,
	- an improperly generated signature, or
	- the node does not have the public key of the node which generated the message.
* If the node does not accept the values of `g`, `p`, or `N` for any reason.

### Connection dropping

When dropping a connection during connection message validation, the following process
is followed in **all** cases:

1. The plaintext message `nope` is transmitted.
2. The connection is then dropped.

It is **not** permitted that a node transmit **any** other information concerning
the cause of the drop.

### Network traffic

After both nodes have exchanged connection messages, they have the necessary information to calculate
the shared session key.

All network traffice past this point for this connection **must** be encrypted using this session key.

Prior to encryption, network data must be padded to the nearest 256 bytes with the method specified
in [RFC 5652][rfc5652].

### Connection overview

The process for establishing a secure connection is as follows:

1. The local node creates a TCP connection to the remote node.
2. The local node sends a connection message to the remote node.
3. The remote node verifies the connection message, dropping the connection if there is an error.
4. The remote node sends a connection message to the local node.
5. The local node verifies the connection message, dropping the connection if there is an error.
6. The local and remote node calculate the shared session key.
7. All future messages are padded and then encrypted using the shared session key.

---

## Request/Response

After a secure connection is established, messages between nodes follow a request/response
approach like a simplified HTTP: all requests are made by the node initiating the connection,
and responses contain the data or some status code.

### Request document

A *request document* is any [YAML message](#message-format) sent by the node which initialized
the network connection, with the following additional requirements:

The request document **requires** the property `request` to be present at the root
of the YAML stream. This property has the following values:

* `node` *[string, required]* The key fingerprint of the node initiating the request.
* `representing` *[string, required]* The key fingerprint of the user which the node is
	representing for this request.
* `type` *[string, required]* The type of request. **Must** be one of the following:
	- `resource` : Requests for a specific resource.
	- `synchronize` : Requests to synchronize resource stacks.

#### `type: resource`

When the `type` property of the `request` object is `resource`, the following additional
fields are **required** on the `request` object:

* `author` : The key fingerprint of the user who published the resource.
* `resource` : The resource identifier of the requested resource.

#### `type: synchronize`

When the `type` property of the `request` object is `synchronize`, the [stack synchronization](#publishsynchronize)
properties **must** be present.

The following additional properties are reserved for use on the `request` object:

* `start_version` *[string, required]* The version of the stack requested to start
	from. This is typically the most recent stack version of the responding node
	as known by the requesting node.
* `maximum_list_size` *[integer, optional]* The requesting node may request the responding
	node limit the resource stack list size in the response.

### Response document

A *response document* is a type of [YAML message](#message-format) sent by a node in
response to a request. There are three *types* of response documents:

* The requested resource, in the case of a resource request, or
* The requested synchronization information, in the case of a synchronization request, or
* A generic response document containing a status, in all other cases.

In the case where the response is the actual resource, it **must** be the **exact** published
resource. No modifications of the resource are allowed, such as inserting additional headers,
and the resource hash and signature should be verified by the receiving node.

At the root of the YAML stream of all other response documents is the **required**
property named `response`, with the following reserved properties:

* `status` *[integer, required]* A status code loosely based on [HTTP status codes][w_httpcodes],
	of one of the following possible values:
	- `102` (Processing) - For throttling purposes, the responding node may use this status code to
		inform the connecting node that the request will take some time. If this status is used,
		the responding node **should** include a `delay` value on the `response` object.
	- `200` (Okay) - The generic response when the request has been processed, and the
		response has no errors.
	- `304` (Not modified) - Used when a node makes a synchronization request and the `stack_version`
		on the `request` object is still the most recent version.
	- `400` (Bad request) - The responding node cannot or will not process the document due to
		something that is perceived to be a client error, such as malformed message syntax, or
		when the requesting node is not authorized for the request action.
	- `404` (Not found) - Used in response to `read` actions, when the resource is not available.
* `delay` *[integer, optional]* If the response `status` is `102`, the responding node **may**
	include this property, which is the delay time in secods. The requesting node should respect
	this delay, and wait for the specified number of seconds to make the same request.

### Stack synchronization

As part of the network protocol, nodes may [synchronize](#publishsynchronize) with each other
their own list of published and known resources. Requests to synchronize set the request type
to `synchronize`.

For all request documents where the request type is `synchronize`, and for all responses
to that request where the response status is `200`, the following properties are **required**
on the `request` object, and on the `response` object:

* `stack` *[object, required]* An object containing the following **required** fields:
	- `current` *[string, required]* The current version of the node's version stack.
	- `list` *[string list, required]* A partial *or* complete list of YAML `key: value`
		pairs which are the values on the node's version stack, where the *key* is the
		resource identifier, and the *value* is the resource author.
	- `start` *[string, required]* The version used for the first value in the `list`
		property, or `false` if the list starts at the zero-th version.
	- `end` *[string, required]* The version of the node's version stack that is calculated
		using the last value in the `list` property. E.g., if the `list` value contains all
		values up to the current version, `end` would be identical to `current`.

Although it is not required that a node calculate intermediate versions for the responding node, the
information provided should be enough that the requesting node could recreate the entire resource stack.

---

## Resources

Resources [published/synchronized](#publishsynchronize) between nodes are [SYML][syml] streams
conforming to the [YAML message](#message-format) restrictions. Additionally, all resources
have at the root of the YAML message the property named `resource`,  with the following
reserved values:

* `user` *[string, required]* The key fingerprint of the user creating the resource.
* `action` *[string, required]* Describes an action to take regarding a resource. **Must** be one
	of the following values: `create`, `read`, `update`, or `delete`.
* `versions` *[string collection, optionally required]* This property is **required** when
	the `action` property is `update` or `delete`, and is a YAML collection of resource
	identifiers of previous versions of the resource.
* `encrypted` *[binary, optional]* Available to any resource. If the resource contains an encrypted
	message, the encrypted data would be in this property. This data is equivalent to an unarmored
	PGP encryption,	encoded as YAML binary.
* `public` *[string, optional]* Public markdown text, e.g. like a blog post. If this property is
	present and not `false`, it indicates that this resource should be considered fully public, e.g.
	like a public facing website page. If this property is not present, it should be interpreted
	as equivalent to being false.
* `control` *[object, optional]* Describes the addition and updating of users and nodes,
	the relationships between users, and the trust level between user and node. This property
	is **required** for all [control resources](#control-resources).

### Resource identifier

The hash of the YAML message which is signed to generate the SYML signature is the *resource identifier*.

### URI: Uniform Resource Identifier

Linking between messages should be done using the [URI][w_uri], which is constructed as follows:

	sdmp://<AUTHOR>[/<RESOURCE>]

* The URI scheme name is `sdmp`.
* `AUTHOR` is the key fingerprint of the user who authored the resource.
* `RESOURCE` is **optional** and is the resource identifier.

URIs have the two possible forms:

* `sdmp://\<AUTHOR>/` - Requests of this type expect the "create user" [control resource](#control-resources)
	of the user whose key fingerprint is `AUTHOR`.
* `sdmp://\<AUTHOR>/\<RESOURCE>` - Requests of this type should return the resource whose
	resource identifier is `RESOURCE` and which has been published by the user
	whose key fingerprint is `AUTHOR`.

### Notes on privacy

Although the focus of the SDMP is not specifically on anonymity, a properly functioning
node application will **not** make visible the recipients of any private message,  for
example by including the recipient user fingerprint. Exposing the recipients of a message
makes the privacy of the users needlessly difficult to protect.

### Etiquette for public vs private

Publishing a resource where the property `public` is present should be interpreted in much
the same way as publishing a public blog post. The information in the resource should be
considered available for general public viewing.

When the property `public` is not present, this case is much like reading an email: the recipient
of the email (or in this case the user viewing the resource) must determine by context and discretion
whether a message should be relayed in whole, in part, or not at all.

Applications should make clear to the user the distinction of public vs private, primarily so
that a user does not accidentally send a private message marked as public.

---

## Control Resources

Control resources are special cases of resources which indicate new users and connections,
creating nodes, and establishing trust levels between nodes.

All control resources **require** the property `control` set on the `resource` object, and
that property is defined by the type of control message.

The following control resources are defined:

### Create a node

The `control` object has the following **required** properties:

* `type` *[string, required]* The exact string: `node`
* `key` *[binary, required]* The public key of the node, encoded as YAML binary data, which
	is equivalent to an unarmored export of an OpenPGP public key.

This resource **must** be signed by the node.

Deleting this resource indicates that this node is no longer secure. Properly functioning
nodes will **not** communicate with a node once this resource is deleted.

Updating this resource is **not** allowed.

### Node information

The `control` object has the following **required** properties:

* `type` *[string, required]* The exact string: `node-info`
* `network` *[list of strings, required]* A list of the network addresses for the node.
* `name` *[string, required]* A human-friendly name for the node.

This resource **must** be signed by the node.

The network addresses **must** be either IPv4 **or** IPv6.

Nodes **should** favor the IPv6 address, where possible.

Network addresses **must** include the port number.

### Node permission levels

Used to indicate a users level of trust in a node. This is what authorizes
a node to publish resources on a users behalf, or synchronize resources that
are intended for a user.

The `control` object has the following **required** properties:

* `type` *[string, required]* The exact string: `node-permission`
* `node` *[string, required]* The key fingerprint of the public key of the node.
* `user` *[string, required]* The key fingerprint of the public key of the user.
* `authority` *[string, required]* Currently only `host` and `publisher` are supported.
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

* `type` *[string, required]* The exact string: `user`
* `key` *[binary, required]* The public key of the user, encoded to YAML binary, which is
	equivalent to an unarmored export of an OpenPGP public key.

This resource **must** be signed by the created user.

Deleting this resource indicates that the user wishes to delete
*all* resources published by that user.

### User information

The `control` object has the following **required** properties:

* `type` *[string, required]* The exact string: `user-info`
* `info` *[object, required]* Any publicly visible information about the user. **Requires**
	at least the following fields:
	- `name` *[string, required]* The publicly visible name of the user.
	- `about` *[string, required]* A brief description of the user.

This resource **must** be signed by the user **or** by any node
authorized by the user as a `publisher`.

### Create connection

When published by a user, this resource indicates that users desire to subscribe to updates
from the connection, and the users willingness to publish updates to that connection.
(See the [publication specifications](#publishsynchronize) for additional details.)

The `control` object has the following **required** properties:

* `type` *[string, required]* The exact string: `connection`
* `user` *[string, required]* The key fingerprint of the user being connected.
* `connection` *[list of strings, required]* A YAML string collection containing at
	least one of the following strings:
	- `publish` : Indicates that messages from the user publishing the resource **may**
		be synchronized to the connection.
	- `subscribe` : Indicates that messages published by the connection **may** be
		synchronized to the user.

This resource **must** be signed by the user **or** by any node authorized by the
user as a `publisher`.

Deleting this resource indicates that the user no longer wishes to synchronize
resources to or from that connection.

---

## Publish/Synchronize

The primary difficulty of publishing messages across a decentralized network is that sending
messages must account for nodes being unpredictably offline.

Since the SDMP is primarily between personal computers instead of dedicated network servers,
this difficulty is a primary concern, and is addressed with the publishing/synchronization
scheme described here.

### Node resource stack

Each node maintains an ordered list of the [resource identifiers](#resource-identifier) of all
resources it publishes, as well as all resources published by any connection. This list is the
pairs of resource identifier and key fingerprint of the user who published the resource.

This list, which is the *node resource stack*, is ordered by knowledge of the resource, instead
of clock time of resource publication. For example, if a node receives knowledge of a published
resource several days after the publication, the resource identifier for that resource is simply
added to the stack.

### Stack versioning

In addition to the resource stack, a version of the stack is calculated after each resource is
added to the stack.

The stack version is a hash, and is calculated by taking the previous stack version, plus the
hash of the new resource, and generating the hash of that.

This means that calculating the hash version of `n` requires calculating the hash of all versions
between `0` and `n`. In other words, where `RIA` is the resource identifier and author, and `SV` is
the stack version at that point, calculating the hash version for `n` looks like:

	SV_1 = hash(RIA_1)
	SV_2 = hash(SV_1,RIA_2)
	SV_3 = hash(SV_2,RIA_3)
	...
	SV_(n-1) = hash(SV_(n-2),RIA_(n-1))
	SV_n = hash(SV_(n-1),RIA_n)

This versioning method is very efficient for adding a new resource to the stack: simply hash the
most recent version and the new resource identifier together.

### Generating the stack version

Generating the stack version is done by hashing the previous version's hash with the new resource
identifier being added.

The hashes used **must** be the string literal of the *lower-cased* hexadecimal value of the hash.

The data hashed to calculate the version is the following exact string literal:

* The previous version hash, as lower-cased hexadecimal, followed immediately by
* A single colon, i.e. `:`, followed immediately by
* The key fingerprint of the author publishing the resource, followed immediately by
* A forward slash, i.e. `/`, followed immediately by
* The resource identifier

This exact string literal is hashed, and that hash is the version hash after adding
the resource.

> **Example:**
>
> If the current version hash is:
>
>	7a5179eecc0fe18760ba615f92603372ae3fe302860098a019e15927551fee3b
>
> And the user key fingerprint is:
>
>	19ee87987129913b74cf6edc1544dbe01c063e3e
>
> And the resource identifier of the resource to be added is:
>
>	98c1eb4ee93476743763878fcb96a25fbc9a175074d64004779ecb5242f645e6
>
> Then the string literal used to generate the next version hash is:
>
> 	7a5179eecc0fe18760ba615f92603372ae3fe302860098a019e15927551fee3b:19ee87987129913b74cf6edc1544dbe01c063e3e/98c1eb4ee93476743763878fcb96a25fbc9a175074d64004779ecb5242f645e6
>
> And the new stack version is (calculated using [this website](http://hash.online-convert.com/sha256-generator)):
>
>	931af3dcdc536b3361b9ae9d7756e80963b7a08638c6e2fc9ad16e3b0e35afb4

### Publishing a resource

A node publishing a resource simply adds the new resource identifier to its own resource stack, and:

* Reports this new version as the latest version to any connecting nodes, and
* Publishes this new stack version to all subscribers.

### Synchronizing a resource

Publishing a resource is technically a purely internal operation of the node, it does not require
that the resource be sent out to any other nodes at that time.

Instead, every time nodes connect they maintain knowledge of the other node's version stack, and
transmit that information on their request. If the connecting node has an outdated version,
the responding node returns their latest version, and the list of resource identifiers and
resource authors necessary for the connecting node to generate the complete version stack
of the responding node.

### Synchronizing to a subscriber

When a new resource is published, the node **should** attempt to connect to all connections which
have authorized themselves as subscribers, and send their updated version stack.

It is the business of the subscriber to request individual resources, either during the same
network connection or in a later network connection.

See the [specifications](#requestresponse) for additional details on the request/response cycle
for completely synchronizing a node's version stack.

[sdmpnpm]: https://github.com/sdmp/sdmp-npm
[relayreader]: http://relayreader.com
[sdmpissues]: https://github.com/sdmp/sdmp.github.io/issues
[sdmppullrequest]: https://github.com/sdmp/sdmp.github.io/pulls
[sdmprepo]: https://github.com/sdmp
[syml]: http://github.com/sdmp/signed-yaml
[vol]: http://veryopenlicense.com/
[fips180]: http://csrc.nist.gov/publications/fips/fips180-2/fips180-2.pdf
[rfc5652]: https://tools.ietf.org/html/rfc5652#section-6.3
[yamlboundaries]: http://www.yaml.org/spec/1.2/spec.html#id2760395
[w_diffiehelman]: https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange
[w_yaml]: https://en.wikipedia.org/wiki/YAML
[w_rsa]: https://en.wikipedia.org/wiki/RSA_(cryptosystem)
[w_sha2]: https://en.wikipedia.org/wiki/SHA-2
[w_aes]: https://en.wikipedia.org/wiki/Advanced_Encryption_Standard
[w_uri]: https://en.wikipedia.org/wiki/Uniform_resource_identifier
[w_httpcodes]: https://en.wikipedia.org/wiki/List_of_HTTP_status_codes
