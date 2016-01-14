---
version: 0.12
layout: spec
title: Synchronization
subtitle: How nodes publish journal updates, and request and respond with resources.
---


The synchronization process for the SDMP is defined in a simple,
network agnostic manner. The protocol can therefore be implemented
over HTTPS, plain TCP, XMPP, using email, or (at least theoretically)
even sent over something like SMS or amateur radio.

---

## Overview

Synchronization between nodes is done by transmitting and receiving
[journal entries](../../journal) and [resources](../../journal/resource).

When a node publishes a resource, it does so by updating its own
journal, and then broadcasting this journal update to all trusted nodes.

If a node is offline for some time, it will become de-synchronized from
the other trusted nodes. In order to stay synchronized, a node will
periodically request journal entry updates from other trusted nodes.

---

## Resource Publishing

When a node publishes a resource, it appends the journal entry to
its own [journal](../../journal), and then at some point in the future
the node broadcasts a [journal update](../../journal/broadcast) to
all [trusted nodes](../../schema/trust).

If a trusted node is not available to receive a journal update, the
broadcasting node should attempt to send a journal update at another
time, and continue trying until the receiving node has been reached
or the broadcasting node gives up.

The backoff algorithm for broadcast retries is not specified here, since
it will likely vary based on the network protocol used, but a simple
exponential decay method is suggested. Remember also that the goal
of the SDMP is simplicity, even at the cost of efficiency, in order
that security can be verified more easily.

---

## Request Update

When a node has been offline for some time, other trusted nodes may
have stopped trying to broadcast updates, in which case it becomes
necessary to request updates manually.

In any case where a trusted node has been inaccesible for a "long"
period of time, a node may send a [journal update request](../../journal/request_journal)
to the trusted node, which should respond with a [response](../../journal/response)
containing a [journal update](../../journal/broadcast).

The definition of "long" is not specified here, since it will likely
vary based on the network protocol used.

---

## Request Resource

After a node receives a journal update, either by broadcast or after
requesting it from a trusted node, it *may* request any unknown resources.
This is done by sending a [resource request](../../journal/request_resource)
to any trusted node has the resource.

A node is considered to have a resource if the resource identifier
is present in that node's journal list.

A node responding to a resource request should yield a
[response](../../journal/response) containing either the
requested resource or some other status code.

---

## Node Journal States

In addition to the [node's journal](../../journal), each node should maintain 
a "last known" and "last transmitted" line identifier for each trusted node.

Each time a node receives a journal update from a trusted node, it would
update the last known line identifier to the last line identifier of the
received journal update.

Each time a node transmits a journal update to a trusted node, it would
update the "last transmitted" line identifier to the last line identifier
of the transmitted journal update.

When [sending](../../journal/broadcast) journal updates the "last transmitted"
would be the `journal.since` property, and the `journal.entries` would
be all journal entries *after* the "last transmitted".

When [requesting](../../journal/request_journal) journal updates, the "last
known" would be the `request.since` property.

---

## Request/Response

Because the core communication protocol is designed to be network agnostic,
there is no requirement that packets sent to a node require any particular
immediate response. Additionally, there are no error responses or timeout
algorithms specified.

However, the following (perhaps somewhat obvious) guidelines should be
followed for any implementation:

* Broadcasting journal updates should be done at some point relatively soon
  after a node publishes a new resource.
* Nodes receiving journal update broadcasts should, at some point soon after
  receiving the broadcast, request unknown resources from the broadcasting
  node *or* from any other trusted node known to have the resource.
* Since nodes may have resources from other nodes, a distributed approach
  should be sought for requesting published resources. Doing so will
  alleviate network traffic for the node publishing the resource.
* Every attempt should be made to implement [forward secrecy][w_forward]. This
  means that network communications should use *session-based* encryption keys.
  A method of generating session keys is specified [here](../../network/shared_key).
* At no point should a node send resource data over the network in an unencrypted
  form. A node sending a communication without first encrypting it should be
  treated as though its keys have been compromised.


[w_forward]: https://en.wikipedia.org/wiki/Forward_secrecy
