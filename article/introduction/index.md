---
layout: article
title: Introduction
subtitle: An informal introduction to this project.
---


The SDMP is a language and network agnostic protocol, used
to distribute encrypted messages between computers or users
without requiring always-on or central servers, and is made
to be simple enough that humans can understand it.

Although a simple protocol can be less efficient, by making
the protocol simple it's easier to make sure that an
implementation is secure.

---

The protocol describes the following:

* Data objects, which are defined JSON objects.
* How to establish a secure network connection.
* Sending messages in a distributed way.

---

There are two "actors" in the protocol:

* Users: Typically humans, possibly organizations or groups.
* Nodes: Applications which synchronize data across the network.

---

The data objects are well defined JSON objects, and most
of them can be validated using a [JSON Schema][jsonschema],
which is a JSON object which defines certain properties to
be required.

An example JSON Schema might look like this:

	{
		"type": "object",
		"properties": {
			"hello": {
				"type": "string",
				"pattern": "^[a-z]+$"
			}
		},
		"required": [ "hello" ]
	}

For this schema, a possible valid JSON object would be:

	{
		"hello": "world"
	}

---

The protocol defines the *container*, which is a JSON
object with some basic properties which describe what
data the object is holding.

The object does this by listing the schema used to validate
itself. To validate a *container* object, the object is
validated against every schema listed.

An example container might look something like this:

	{
		"sdmp": {
			"version": "{{site.version}}",
			"schemas": [
				"URI to JSON Schema 1",
				"URI to JSON Schema 2"
			]
		},
		"someOtherData": { ... }
	}

In this example, `JSON Schema 1` and `JSON Schema 2` would
be used to validate the properties of the object.

Since the schemas are known, the application is able to
interpret and use the properties of the object.

---

Although anyone can make a schema and reference it in a
container by a full URI, the SDMP defines a handful of
schemas which are core to the protocol, and which are
referenced by name. These core schemas must be understood
by any software implementation.

The main schemas are:

* `identity`: Public key for an identity.
* `node`: Network connection details for a node.
* `user`: Information about a user.
* `encrypted`: For encrypted data.
* `signature`: Data and its cryptographic signature.
* `trust`: Establish a trust between two identities.
* `resource`: A published container.
* `revoke`: Revoke a trust or a resource.
* `message`: Human readable messages between users.
* `receipt`: Let a sender know that you received a resource.

Additional core schemas, used for network operations:

* `shared_key`: Establish a session key between nodes.
* `broadcast`: Periodic status updates sent to nodes.
* `request_journal`: Request status updates from a node.
* `request_resource`: Request published resources from a node.
* `response`: Respond to any requests.

All of these schemas are JSON objects containing specific
properties, used for operations in the protocol.

---

Network connections are established by *nodes* through a
simple handshake which uses the [Diffie-Hellman][dh]
algorithm, which creates a new key per connection. This
is done to achieve [forward secrecy][forward].

Each node sends the other node a `shared_key` object, which
contains half of the numbers needed for the Diffie-Hellman
calculation. At this point, each node has enough information
to calculate a shared session key.

After establishing a shared session key, each node sends
a `signature` object, signing the hash of the previously
sent `shared_key` object, thus proving their identity.

---

Identities are created by generating public/private key
pairs, and then publishing the public key to other users.

A user creates their own identity, and then creates additional
identities for each application used to synchronize data
across the network. These additional identities must be
given authorization by the user, in the form of a signed
`trust` object, which limits the actions the application's
identity can take.

For example, a user might authorize the application on
their laptop to read and write public and private data,
but authorize the application on a shared server to only
synchronize data.

---

Users send data to other users by publishing encrypted
resources, encrypting the data to the end user *and* to
every node the end user has authorized.

Messages are published by appending the resource to a
journal, which is a list of known resources maintained
by each identity.

Updates to this list are periodically sent out to all
known identities, and those identities are responsible
for requesting resources they do not have.


[jsonschema]: http://json-schema.org/
[dh]: https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange
[forward]: https://en.wikipedia.org/wiki/Forward_secrecy
