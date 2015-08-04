---
layout: docs
title: Synchronization
subtitle: How nodes publish journal updates, and request and respond with resources.
---


The synchronization process for the SDMP is defined in a way that makes it network agnostic.
The SDMP can therefore be implemented over HTTPS, [plain TCP](../tcp/), sent in an email, or
(at least theoretically) even sent over SMS or low-bandwidth amateur radio.

---

## Overview

Synchronization between nodes is done by transmitting and receiving [journal lists](../journal/)
and [resources](../resource/).

The two core actions of the SDMP are:

* Nodes may request journal list updates from other nodes, and may
  periodically send journal list updates to other nodes.
* Nodes may request specific resources from other nodes, and respond
  with those resources.

---

## Request/Response Messages

All request/response messages between nodes must be valid [SDMP containers](../container/),
and require one of the following schemas as the *first* schema in the container payload
object `sdmp.schemas` property:

* **[`journal_request`](#journal-request) ([schema](schema_journal_request)):** Used by a node
  to request a journal update from another node.
* **[`journal_update`](#journal-update) ([schema](schema_journal_update)):** Used as a response
  to a journal request, or by a node sending a periodic journal update to another node.
* **[`resource_request`](#resource-request) ([schema](schema_resource_request)):** Used by a node
  to request a specific resource from another node.
* **[`resource_response`](#resource-response) ([schema](schema_resource_response)):** Used as a
  response to a resource request, or by a node sending a resource to another node.

---

## Node Journal States

In addition to the [node's journal](../journal/), each node maintains a *last known* and
*last transmitted* line number for each connection's known nodes.

For example, if a user has a single node and 10 connections, and if each connection has
2 nodes, the user's node would maintain the *last known* and *last transmitted* line
number for a total of 20 nodes.

Each time a node receives a journal update from another node, it updates the *last known*
line number to the last line number of the received journal update.

Each time a node transmits a journal update to another node, it updates the *last transmitted*
line number to the last line number of the transmitted journal update.

---

## Journal Request

Nodes may request journal entries from other nodes. This may be done if a node
has been offline for a while, or other technical reasons. The expected response
to a journal request is a [journal update](#journal-update).

Journal requests have the following properties:

###### `request` *(object, required)*

Holds the request properties. This contains the following reserved properties:

###### `request.type` *(string, required)*

Indicates the request type. For the journal request schema, this must be the string `journal`.

###### `request.since` *(string, required)*

This property is the *last known* [journal line number](../journal/#journal-line-number) of
the requesting node. A successful response to this request would have the journal entries
immediately after this entry, and would not include this line number.

###### `request.limit` *(integer, optional)*

The *maximum* number of journal line number entries to include in the response. The
responding node may not exceed this limit, but if this value is not defined in the
request, the responding node may choose whatever limit it decides is appropriate.

---

## Journal Update

This schema is used when a node responds to a [journal request](#journal-request), or when
a node periodically sends a journal update to other nodes.

Journal responses have the following properties:

###### `response` *(object, required)*

Holds the response properties. This contains the following reserved properties:

###### `response.type` *(string, required)*

Indicates the response type. For the journal response schema, this must be the string `journal`.

###### `response.since` *(string, required)*

This property is the [journal line number](../journal/#journal-line-number) of the line
immediately preceding the first entry in the journal entries list of this response.

(E.g., if the first entry of the journal entry list in this response were line
number `n`, this value would be `n-1`.)

###### `response.entries` *(ordered string list, required)*

An ordered list of valid [journal entries](../journal/#journal-entries).

---

### Resource Request

Nodes may request specific resources from other nodes.

Resource requests have the following properties:

###### `request` *(object, required)*

Holds the request properties. This contains the following reserved properties:

###### `request.type` *(string, required)*

Indicates the request type. For the resource request schema, this must be the string `resource`.

###### `request.identifier` *(string, required)*

Responses to this resource request will include this identifier in the response object. The
string may contain only up to 40 ASCII characters.

Implementations of the SDMP should attempt to make this property unique per request and per node.

###### `request.author` *(string, required)*

The [key fingerprint](../cryptography/#key-fingerprint) of the *user* who published
the resource being requested.

###### `request.resource` *(string, required)*

The [resource identifier](../resource/#resource-identifier) of the resource being requested.

---

## Resource Response

This schema is used when a node responds to a [resource request](#resource-request), and
any time a node sends a resource to another node.

Resource responses have the following properties:

###### `response` *(object, required)*

Holds the response properties. This contains the following reserved properties:

###### `response.type` *(string, required)*

Indicates the response type. For the resource response schema, this must be the string `resource`.

###### `response.status` *(number, required)*

A [status code](#response-status-code), similar to HTTP status codes, which
indicate the type of response.

###### `response.description` *(string, optional)*

An optional string to hold a human-readable description of the response object. The string
may contain only up to 40 ASCII characters.

###### `response.request` *(string)*

If the response object is in response to a [resource request](#resource-request), this
property must be the value of the `request.identifier`. In all other cases, this
property must not be set.

###### `response.content` *(object)*

If the response contains a valid [resource object](../resource/), this object is the
complete [SDMP container](../container/). In all other cases, this property must not
be set.

---

## Response Status Code

The SDMP specifies the meaning of the following status codes, which are similar to HTTP
status codes. If you desire additional status codes for your application and want more
widespread adoption, please make a request by [filing an "issue" on Github][newissue].

* `200` **"OK"** - Used to indicate a response object containing a valid resource.
* `400` **"Bad request"** - Used to indicate that a received request was not interpretable.
* `404` **"Not found"** - This status is used in all cases where a requested resource
  is unavailable or when the resource has been deleted.

---

## Request/Response

Because the core communication protocol is designed to be network agnostic, there can be no
requirement that packets sent to a node require any particular response. Additionally, there are
no error responses or timeout algorithms specified. (The [SDMP TCP implementation](../tcp/)
has some implementation details, but they are network specific, and are not considered part
of the core protocol.)

However, the following guidelines should be followed for any implementation:

* Journal update broadcasts should be made at some point relatively soon after a node
  publishes a new resource.
* Nodes receiving journal update broadcasts should, at some point soon after receiving the
	broadcast, request unknown resources from the broadcasting node *or* from any node
	known through a connection. This aids in network load distribution.
* Since nodes may have resources from other nodes, a distributed approach should be sought for
	requesting published resources. Doing so will alleviate network traffic from the node
	publishing a new resource.
* Every attempt should be made to implement [forward secrecy][w_forward]. This means that
	network communications should use *session-based* encryption keys. See the
	[SDMP TCP specifications](../tcp/) for an example implementation over TCP.
* At no point should a node send resource data over the network in an unencrypted form. A node
	sending a communication without first encrypting it should be treated as though its keys have
  been compromised.


[w_forward]: https://en.wikipedia.org/wiki/Forward_secrecy
[schema_journal_request]: https://github.com/sdmp/sdmp-schema/blob/master/schemas/journal_request.json
[schema_journal_update]: https://github.com/sdmp/sdmp-schema/blob/master/schemas/journal_update.json
[schema_resource_request]: https://github.com/sdmp/sdmp-schema/blob/master/schemas/resource_request.json
[schema_resource_response]: https://github.com/sdmp/sdmp-schema/blob/master/schemas/resource_response.json
