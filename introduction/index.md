---
layout: article
title: Intro to the SDMP
subtitle: A birds eye view of what makes the SDMP tick.
---


If you are looking for a less formal overview of the SDMP, this
is probably where you should start.

Please note that, although this article will be kept up to date
as much as possible, it may not match the actual specifications
exactly.

If you see any errors or have questions or suggestions, please
do not hesitate to let us know by
[filing an issue](https://github.com/sdmp/sdmp.github.io/issues)
and we'll respond as soon as we can!

---

# Overview

A user runs an application (a "node") on their computer, and sends a message
to another user by publishing a "resource" to all trusted users.

It is anticipated that (until the protocol becomes widely used) identities
will initially be exchanged "out of band" of the network. For example, keys
might be exchanged over email and verified in person.

## Nodes and Users

A "user" is the human (or organization) who maintains an account, while
a "node" is an application that is run on some hardware.

For example, I am the "user", and my laptop and smartphone each run a
seperate "node".

Nodes are given specific authorization through a published trust, such
as the ability to read and write private messages, or limited to the
ability to only pass along public messages.

## Containers and Resources

All the data objects core to the protocol are defined JSON objects.

The foundational object is a "container", which is a simple JSON
object that defines what data it holds by specifying what schemas
it contains.

Published resources are containers which have signed data in them,
which is another container object. E.g., a published resource looks
something like this JSON object (data has been truncated with ellipsis
for readability):

	{
		"sdmp": {
			"version": "0.11.0",
			"schemas": [ "signature" ]
		},
		"signature": {
			"identifier": "5w4IzlVU...fStdlN4A",
			"payload": "eyJzZG1w...RPT0ifX0",
			"signatures": [{
				"protected": "OiJIUzUx...mRFZNX3k",
				"signature": "cp1okHSf...8qp8Q4xk"
			}]
		}
	}

## Identity

The identity of a user or node is tied to a single public/private
key pair. The public section of this key pair is placed into a resource
and made available to other users.

An identity should be considered "public" information in a manner
similar to an email address: other users and nodes will likely pass
along identity resources to other trusted users and nodes.

## User Relationships

Users tell other users whether they know each other by publishing a
trust authorizing that relationship. This resource is synchronized
out to the trusted users and nodes of the user publishing the resource.

## Node Authorization

A user authorizes a node to take certain actions on behalf of the user.

For example, a person might authorize the node application installed on
their laptop to publish content. Doing this means that the users main
identity private key does not need to be installed on the laptop.

Alternately, a person could authorize a node application installed on a
remote server to only host content. Doing this would allow an always-on
server to speed up synchronization (much like an IMAP or XMPP server)
without giving the remote node access to private messages.

## Node Behaviour

Nodes communicate by connecting directly to other trusted nodes and
transmitting content over an encrypted network.

For example, if my laptop runs a node application on my home network, and
my friend is running a trusted node on a laptop at a coffee shop, we would
need to be able to tunnel through the network (using port forwarding or
similar techniques) to connect directly to each other.

It is anticipated that some users will maintain remote servers, able to
be accessed without port forwarding, as a method of increasing network
robustness and speeding up message synchronization.

## Node To Node Trust

Two nodes may connect to each other if the users who authorized the
nodes have indicated with a published trust that the two users know
each other. A node should not establish a network connection unless
it has validated a trust with the connecting node.

For example, a trust is established in this way:

* If user `A` publishes a trust with node `X`, and
* user `B` publishes a trust with node `Y`, and
* user `A` and `B` have published a trust with each other

then nodes `X` and `Y` have a validated trust, and can connect to each other.

## The Journal

Each node maintains its own journal, which is a list of resources it knows
about. Note that the list is not ordered by publication date--it is ordered
by the date the node receives and validates the resource.

## Publishing Resources

A user creates a resource and "publishes" it with a node. Since the node
validates the resource at the time it is published, it is added to the
node's journal.

Each node periodically synchronizes the journal entry list (not the
resource entry objects) to all other trusted nodes. Each node is then
responsible for requesting resources it has not previously acquired.

This distribution mechanism is basically the publisher/subscriber pattern.

## Synchronization

When a node receives a journal entry list, if there are resources
in that entry list that have not yet been acquired, the node should
proceed to request them from any node containing that resource in
their journal list.

This mechanism is the synchronization which allows the propagation of
content to be distributed across multiple nodes, making the network
more robust. It also means that the publishing node does not necessarily
need to upload a copy to each trusted node--resources may be downloaded
from any node which has the resource.

## Sending Private Messages

A user sends a private message to other users by publishing an
encrypted resource. This means that all other trusted nodes (not
just the recipient node) will eventually acquire the private,
encrypted message.

When the recipient node eventually acquires the published resource,
either from the publishing node or some other node, the recipient
node should send a receipt to the sender (inside an encrypted resource).

## Shared Symmetric Key

In order to establish communication, either over this protocol or
some other, a handshake protocol is given which uses the Diffie-Helman
algorithm to establish a shared, signed, symmetric key.

## Network Connection Handshake

All network communication must be done over an encrypted connection. The
handshake protocol establishes the shared symmetric key, and this key
is used to encrypt all network traffic during that connection.
