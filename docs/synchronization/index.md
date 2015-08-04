TODO: Those JSON objects should be made into schemas and be published over at sdmp-schema.
---
layout: docs
title: Synchronization
subtitle: How nodes publish journal updates, and request and respond with resources.
---


The synchronization process for the SDMP is defined in a way that makes it network agnostic.
Publishing and requesting resources can be done over HTTPS, plain TCP, sent in an email, or
(at least theoretically) even sent over SMS.

This document describes the method of publishing and requesting resources, and the JSON
objects used to communicate those actions.

## Overview

Communication between nodes is done by transmitting and receiving the following message types:

* journal list updates
* journal list request/response
* resource request/response

### Journal list updates

When a node publishes a resource, and when it becomes aware of resources published by
[connections](../resource/#relationship), it writes those as entries in its
[journal](../journal/#journal-entries).

Periodically, a node will send out the "most recent" list of journal entries to all other
known nodes. It is this "most recent" list that is the  main data of the *journal list update*.

If the sending node receives confirmation that the receiving node has received the journal
entry list, the last [journal line number](../journal/#journal-line-number) is recorded for
that node. Each node maintains the last journal line number sent to every other known node.

### Most recent list

Because the publishing node knows the last journal line number transmitted to the receiving
node, when a node sends the "most recent list", it sends 























## Communication document properties

All communication documents have, at the root of the YAML document, a single object
named `sdmp` with the following reserved properties:

###### `sdmp.version` *(number, required)*

The version of the SDMP specifications used to generate the communication document. Currently
this value is the number `0.9` exactly.

###### `sdmp.node` *(string, required)*

The [key fingerprint](../cryptography/) of the node that generated the communication
document. This is equivalent to the SHA-256 hash of the node public key, encoded to
lower-cased hexadecimal.

###### `sdmp.created` *(string, required)*

The [ISO 8601][w_iso8601] formatted date that the resource was created, with granularity to the
minute of the hour, e.g. `2015-04-25 13:10`. This value is primarily used for human readability,
since it cannot be cryptographically verified.

###### `sdmp.communication` *(object, required)*

Used to indicate the type of request or response communication document.

### Journal list request

Requests for journal lists have the following additional properties on
the `sdmp.communication` object.

###### `sdmp.communication.synchronize` *(object)*

Indicates that the document is a request for a node's journal list. This object
is *required* in journal list requests.

###### `sdmp.communication.synchronize.since` *(string, required)*

This property is the [journal line number](../journal/#journal-line-number) of the
last line "known" to the requesting node. A successful response to this request would
have the journal entries immediately after this entry.

###### `sdmp.communication.synchronize.limit` *(integer, optional)*

The *maximum* number of entries to include in the response. If this value is not included
in the request, the responding node may choose whatever limit it decides is appropriate.

### Journal list response and update

Responses to journal list requests and journal list update broadcasts have the
following additional properties on the `sdmp.communication` object.

###### `sdmp.communication.journal` *(object)*

Indicates that the document contains a list of journal entries. This object is
*required* in responses to journal list requests, as well as in journal broadcasts
sent when the node published a new resource.

###### `sdmp.communication.journal.since` *(string, required)*

This property is *required* in journal entry lists. It is the
[journal line number](../journal/#journal-line-number) of the line immediately
preceding the first entry in the document journal list.

(If the first entry of the journal entry list in the document were line
number `n`, this value would be `n-1`.)

###### `sdmp.communication.journal.entries` *(ordered string list, required)*

An ordered list of valid [journal entries](../journal/#journal-entries).

If the journal list request included the `limit` property, the response must not
have more than the integer value of `limit`.

### Resource request

Requests for individual resources have the following additional properties on
the `sdmp.communication` object.

###### `sdmp.communication.request` *(object)*

Indicates that the document is a request for a single resource.  This object
is *required* in resource requests.

###### `sdmp.communication.request.author` *(string, required)*

The [key fingerprint](../cryptography/#key-fingerprint) of the user who published
the resource being requested.

###### `sdmp.communication.request.resource` *(string, required)*

The [resource identifier](../resources/#resource-identifier) of the resource being requested.

### Resource request response

The resource request response is the exact, unmodified [resource](../resource/).

### General node response

There are potentially many cases where a node wishes to respond with additional or different
data, e.g. with rate limiting information, or if a requested resource is unavailable.

When this occurs, the node may respond with a resource response. These have the following
additional properties on the `sdmp.communication` object.

###### `sdmp.communication.response` *(object)*

Indicates that the document is a node-to-node response, used for network traffic control
or other internal information.

###### `sdmp.communication.response.request` *(string, optional)*

If the response is related to a request, this field is used to indicate which request this
document is in response to.

This is equivalent to the resource identifier of the request, that is, the [hash](../cryptography)
of the YAML request encoded as lower cased hexadecimal.

###### `sdmp.communication.response.status` *(integer, required)*

A [status code](#response-status-codes), similar to HTTP status codes, which
indicate the type of response.

###### `sdmp.communication.response.description` *(string, optional)*

A human readable description of the returned status.

## Response status codes

The SDMP specifies the meaning of the following status codes, which are similar to HTTP
status codes. If you desire additional status codes for your application and want more
widespread adoption, please make a request by [filing an "issue" on Github][newissue].

* `400` **"Bad request"** - Used to indicate that a received communication document was
	not interpretable. The property `sdmp.communication.response.request` is *required* here.
* `404` **"Not found"** - This status is used in all cases where a requested resource
	is unavailable or when the resource has been deleted.
* `408` **"Request timeout"** - If no data has been received by a node within some reasonable
	time, it may transmit this status and then drop the connection.

## Journal update broadcast

After a node publishes a new resource and updates it's [journal list](../journal/), it *may*
send journal updates to known connection nodes.

## Request/Response

Because the core communication protocol is designed to be network agnostic, there can be no
requirement that packets sent to a node require any particular response. Additionally, there are
no error responses or timeout algorithms specified. (The [basic network specifications](../network/)
has some implementation details, but they are network specific, and are not considered part
of the core protocol.)

However, the following guidelines should be followed for any implementation:

* Journal update broadcasts are made at some point soon after a node publishes a new resource.
* Nodes receiving journal update broadcasts do not need to request any resources listed in
	a journal update.
* Nodes receiving journal update broadcasts should, at some point soon after receiving the
	broadcast, request unknown resources from the broadcasting node *or* from any node
	known through a connection.
* Since nodes may have other nodes resources, a distributed approach should be sought for
	requesting published resources. Doing so will alleviate network traffic from the node
	publishing a resource.
* Every attempt should be made to implement [forward secrecy][w_forward]. This means that
	network communications should use session-based encryption keys. See the
	[basic network specifications](../network/) for an implementation over TCP.
* At no point should a node send message data over the network in an unencrypted form. A node
	sending a communication document without first encrypting it *must* be treated as though
	its keys have been compromised.

[w_forward]: https://en.wikipedia.org/wiki/Forward_secrecy
[syml]: http://github.com/sdmp/signed-yaml
[w_iso8601]: https://en.wikipedia.org/wiki/ISO_8601
[yaml_10_delimiter]: http://yaml.org/spec/1.0/#id2489959
[yaml_11_delimiter]: http://yaml.org/spec/1.1/#id857577
[yaml_12_delimiter]: http://www.yaml.org/spec/1.2/spec.html#id2760395
