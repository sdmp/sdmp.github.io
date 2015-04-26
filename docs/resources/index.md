---
layout: index
version: 0.9
---


All resources published and made available in the SDMP are signed YAML streams, as described
by the [SYML][syml] specifications. This document defines the types of YAML streams possible
within the SDMP.

## Resource

Where the term `resource` is used, it is meant a valid [SYML][syml] stream with the
following additional restrictions:

* Multi-document YAML streams are **not** allowed.
* The YAML stream must use the start and end boundary characters, `---` and `...`,
	respectively. (See the [YAML specs][yamlboundaries] for more information on
	the boundary characters.)
* Comments inside the YAML stream are **not** allowed.
* More precisely, **only** values which are actual YAML properties or values are allowed.

## Resource Properties

All resources have, at the root of the YAML document, a single object named `sdmp` with the
following reserved properties:

###### `sdmp.version` *(number, required)*

The version of the SDMP specifications used to generate the resource. Currently this
value is the number `0.9` exactly.

###### `sdmp.publisher` *(string, required)*

The [key fingerprint][TODO] of the user or node that published the resource. This is
equivalent to the SHA-256 hash of the public key, encoded to lower-cased hexadecimal.

###### `sdmp.created` *(string, required)*

The [ISO 8601][w_iso8601] formatted date that the resource was created, with granularity to the
minute of the hour, e.g. `2015-04-25 13:10`. This value is primarily used for human readability,
since it cannot be cryptographically verified.

###### `sdmp.updates` *(list, string, optional)*

Used to indicate a revocation of a previous resource, being replaced with this resource.

Each element of the list is the [resource identifier][TODO] of the resource being identified,
encoded to lower-case hexadecimal.

## Resource Types

There are three fundamental resource types used within the SDMP: identity documents, relationship documents,
and all others.

Of general documents, there are three document schemas which are specified within this protocol, and may be used
individually or combined within a single resource: User Information, Node Information, and Encrypted Content.

### Identity Documents

Used to create a user or node, this document contains the public key used to identify the
user or node.

The `sdmp` object in the resource's YAML document has the following reserved properties:

###### `sdmp.identity` *(object)*

Holds the identity properties. This contains the following reserved properties:

###### `sdmp.identity.type` *(string, required)*

Defines the type of identity. The two supported types are `user` and `node`.

###### `sdmp.identity.expires` *(string, required)*

The [ISO 8601][w_iso8601] formatted date that the identity key should be expired, with granularity
to the minute of the hour, e.g. `2015-04-25 13:10`.

Note: An application should not use this key after the date specified, and should not consider
messages signed with this key to be valid if they are believed to be generated after this date.

###### `sdmp.identity.key` *(binary, required)*

The public key of a unique [RSA][w_rsa] key-pair of at least 2048 bits in length, encoded
to [YAML binary][yaml_binary].

### Relationship Documents

Used to indicate the relationship between the publishing user and another single user, or the publishing
user and a single node.

The `sdmp` object in the resource's YAML document has the following reserved properties:

###### `sdmp.relationship` *(object)*

Used to store information about the user-to-user or user-to-node relationship.

###### `sdmp.relationship.type` *(string, required)*

Defines the type of relationship. The three supported types are `connection` (user to user), `host`
(user to node--the node does not have publishing privileges), `publisher` (user to node--the node has
publishing privileges).

###### `sdmp.relationship.user` *(string)*

When the relationship type is `connection`, this property is required.

The [key fingerprint][TODO] of the user the resource publisher is connecting to.

###### `sdmp.relationship.node` *(string)*

When the relationship type is `host` or `publisher`, this property is required.

The [key fingerprint][TODO] of the node the resource publisher is authorizing.

### User Information Documents

Used to store information about a user.

(Note: A resource containing a user information document may also contain encrypted content.)

The `sdmp` object in the resource's YAML document has the following reserved properties:

###### `sdmp.user` *(object)*

Contains information about the user.

###### `sdmp.user.name` *(string)*

A human-readable name for the user.

###### `sdmp.user.about` *(string)*

A human-readable description of the user. Meant to differentiate between users with similar names.

### Node Information Documents

Used to store information about a node.

(Note: A resource containing a node information document may also contain encrypted content.)

The `sdmp` object in the resource's YAML document has the following reserved properties:

###### `sdmp.node` *(object)*

Contains information about the node.

###### `sdmp.node.name` *(string)*

A human-readable description of the node type.

###### `sdmp.node.ips` *(list, string)*

Each element of the list is the string representation of the IP address and port at which the
node is reachable. This address may be IPv4 or IPv6.

### Encrypted Content

Used to transmit encrypted data in a secure form, this document contains the content encrypted using
a single-use [session key][TODO], and that key is encrypted using each recipients public identity key.

The [session key][TODO] must be an [AES][w_aes] compatible key of at least 256 bits.

For encrypted content, the `sdmp` object in the resource's YAML document has the following reserved properties:

###### `sdmp.encrypted` *(object)*

Holds the keys and encrypted content. This contains the following reserved properties:

###### `sdmp.encrypted.keys` *(list, binary, required)*

Each element of the list contains the single-use [session key][TODO], encrypted using the recipients
public identity key, and encoded to [YAML binary][yaml_binary].

###### `sdmp.encrypted.data` *(binary, required)*

The content, encrypted to the single-use [session key][TODO].

### Visible Content

Used to transmit any general data in an insecure form (such as a public blog or similar).

For visible content, the `sdmp` object in the resource's YAML document has the following reserved properties:

###### `sdmp.document` *(object)*

Holds any general non-encrypted content.

## Examples

A user identity resource:

	---
	sdmp:
		version: 0.9
		publisher: 7a5179eecc0fe18760ba615f92603372ae3fe302860098a019e15927551fee3b
		created: 2015-04-23
		identity:
			type: user
			expires: 2020-04-23
			key: !!binary |
				FleoLoFuof+Omcib9j3xnxKtmuZ1pn5cKBcadZhmObKDSodzg81lUHx32FSAOWs2HRiTBdmg
				...truncated...
				Dc9xsypBiKcAoL0KUD/QA9H/T2ZgsLR2/HaBC0RY
	...

A node identity resource:

	---
	sdmp:
		version: 0.9
		publisher: 79eecc0fe18760ba615f92603372ae3fe302860098a019e15927551fee3b7a51
		created: 2015-04-23
		identity:
			type: node
			expires: 2020-04-23
			key: !!binary |
				of+Omcib9j3xnxKtmuZ1pn5cKBcadZhmObKDSodzg81lUHx32FSAOWs2HRiTBdmgFleoLoFu
				...truncated...
				iKcAoL0KUD/QA9H/T2ZgsLR2/HaBC0RYDc9xsypB
	...

A user-to-user relationship resource:

	---
	sdmp:
		version: 0.9
		publisher: 7a5179eecc0fe18760ba615f92603372ae3fe302860098a019e15927551fee3b
		created: 2015-04-23
		relationship:
			type: connection
			user: 79603372ae3fe302860098a019e15927551fee3b7a51cc0fe18760ba615fee92
	...

A user-to-node host relationship resource:

	---
	sdmp:
		version: 0.9
		publisher: 7a5179eecc0fe18760ba615f92603372ae3fe302860098a019e15927551fee3b
		created: 2015-04-23
		relationship:
			type: host
			node: 79eecc0fe18760ba615f92603372ae3fe302860098a019e15927551fee3b7a51
	...

A user-to-node publisher relationship resource:

	---
	sdmp:
		version: 0.9
		publisher: 7a5179eecc0fe18760ba615f92603372ae3fe302860098a019e15927551fee3b
		created: 2015-04-23
		relationship:
			type: publisher
			node: 79eecc0fe18760ba615f92603372ae3fe302860098a019e15927551fee3b7a51
	...

A user information resource:

	---
	sdmp:
		version: 0.9
		publisher: 7a5179eecc0fe18760ba615f92603372ae3fe302860098a019e15927551fee3b
		created: 2015-04-23
		user:
			name: John Doe
			about: Lumberjack who has a wonderful wife and newborn baby. The baby is also a lumberjack.
	...

A node information resource:

	---
	sdmp:
		version: 0.9
		publisher: cc0fe18760ba615f92603372ae3fe302860098a019e15927551fee3b7a5179ee
		created: 2015-04-23
		node:
			name: John's laptop
			ips:
				- 170.23.64.217:36074
				- [2607:f298:1:126::72c:b051]:36075
	...

A resource with encrypted content:

	---
	sdmp:
		version: 0.9
		publisher: 7a5179eecc0fe18760ba615f92603372ae3fe302860098a019e15927551fee3b
		created: 2015-04-23
		encrypted:
			keys:
				- !!binary |
					yeoaGcMd00nipGWs/JNXpkgnWCfjaVAYEL0b8gkMG0ZbrLVqstsHeTJtCe4n2ZAuc4iyfkRO
					...truncated...
					cADh4F3LXD1JapGUvMpvFKCymSde6Cewr1ZDeJ2w4wCg8sLfOlHN0UA8hoYEsgPIU4nIGqcE
				- !!binary |
					LoFuof+Omcib9j3xnxKtmuZ1pn5cKBcadZhmObKDSodzg81lUHx32FSAOWs2HRiTBFleo0Lf
					...truncated...
					AJdjskO+eCpGOozIAkJ4NfBWy5KkiAS91OPN9Idcmf101pLuIHTrdhQnsxJ17L4t5c6l41S6
			data: !!binary |
				9MO2m3GejxuwZRanuQVKQttgCgNE1pqvWAKukzOscslZ6YvRlKJJD38AAwUEAJDD01+Jb1pL
				...truncated...
				BEpBGHv5V9yq9yrelLwO4pcaMCNf8dcp1eImPik61JKZIpEjPfxtexRCFgoy71tgrNRHkM4q
	...

A resource with public content:

	---
	sdmp:
		version: 0.9
		publisher: 7a5179eecc0fe18760ba615f92603372ae3fe302860098a019e15927551fee3b
		created: 2015-04-23
	blog:
		author:
			name: John Doe
			hash: 7a5179eecc0fe18760ba615f92603372ae3fe302860098a019e15927551fee3b
		data: |
			This is an imagined *markdown* formatted thing, like a blog post or whatnot.
	...

An update to the previous resource, if it had two existing versions:

	---
	sdmp:
		version: 0.9
		publisher: 7a5179eecc0fe18760ba615f92603372ae3fe302860098a019e15927551fee3b
		created: 2015-04-23
		updates:
			- 615f92603372ae3fe302860098a019e15927551fee3b7a5179eecc0fe18760ba
			- cc0fe18760ba615f92603372ae3fe302860098a019e15927551fee3b7a5179ee
			- e18760ba615f92603372ae3fe302860098a019e15927551fee3b7a5179eecc0f
	blog:
		author:
			name: John Doe
			hash: 7a5179eecc0fe18760ba615f92603372ae3fe302860098a019e15927551fee3b
		data: |
			This is a *markdown* formatted blog post that I wrote.
	...

To remove a resource, simply create an update with no content:

	---
	sdmp:
		version: 0.9
		publisher: 7a5179eecc0fe18760ba615f92603372ae3fe302860098a019e15927551fee3b
		created: 2015-04-23
		updates:
			- 79eecc0fe18760ba615f92603372ae3fe302860098a019e15927551fee3b7a51
			- cc0fe18760ba615f92603372ae3fe302860098a019e15927551fee3b7a5179ee
			- e18760ba615f92603372ae3fe302860098a019e15927551fee3b7a5179eecc0f
			- 615f92603372ae3fe302860098a019e15927551fee3b7a5179eecc0fe18760ba
	...

Mixing encrypted and visible content is allowed:

	---
	sdmp:
		version: 0.9
		publisher: 7a5179eecc0fe18760ba615f92603372ae3fe302860098a019e15927551fee3b
		created: 2015-04-23
		encrypted:
			keys:
				- !!binary |
					yeoaGcMd00nipGWs/JNXpkgnWCfjaVAYEL0b8gkMG0ZbrLVqstsHeTJtCe4n2ZAuc4iyfkRO
					...truncated...
					cADh4F3LXD1JapGUvMpvFKCymSde6Cewr1ZDeJ2w4wCg8sLfOlHN0UA8hoYEsgPIU4nIGqcE
				- !!binary |
					LoFuof+Omcib9j3xnxKtmuZ1pn5cKBcadZhmObKDSodzg81lUHx32FSAOWs2HRiTBFleo0Lf
					...truncated...
					AJdjskO+eCpGOozIAkJ4NfBWy5KkiAS91OPN9Idcmf101pLuIHTrdhQnsxJ17L4t5c6l41S6
			data: !!binary |
				9MO2m3GejxuwZRanuQVKQttgCgNE1pqvWAKukzOscslZ6YvRlKJJD38AAwUEAJDD01+Jb1pL
				...truncated...
				BEpBGHv5V9yq9yrelLwO4pcaMCNf8dcp1eImPik61JKZIpEjPfxtexRCFgoy71tgrNRHkM4q
	blog:
		author:
			name: John Doe
			hash: 7a5179eecc0fe18760ba615f92603372ae3fe302860098a019e15927551fee3b
		data: |
			This is a *markdown* formatted blog post that I wrote.
	...

[syml]: http://github.com/sdmp/signed-yaml
[yamlboundaries]: http://www.yaml.org/spec/1.2/spec.html#id2760395
[yaml_binary]: http://yaml.org/type/binary.html
[w_aes]: https://en.wikipedia.org/wiki/Advanced_Encryption_Standard
[w_iso8601]: https://en.wikipedia.org/wiki/ISO_8601
[w_rsa]: https://en.wikipedia.org/wiki/RSA_(cryptosystem)
