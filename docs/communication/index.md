---
layout: docs
title: Communication
subtitle: How nodes publish journal updates, and request and respond with resources.
---


The communications within the SDMP are network-agnostic. They can be communicated over
HTTP, plain TCP, sent in an email, or even sent over SMS.

Communication between nodes is done by transmitting and receiving the following signed
and encrypted document types: journal list requests, journal list responses and updates,
and resource requests and responses.

## Communication document format

All communication between SDMP nodes must be valid SYML streams, as described
by the [SYML specifications][syml], with the following additional restrictions:

* The YAML stream must start with three dashes (e.g., `---`) as shown in
	[YAML 1.0][yaml_10_delimiter], [YAML 1.1][yaml_11_delimiter], and [YAML 1.2][yaml_12_delimiter].
* The YAML stream must be terminated with the three periods (e.g. `...`) as shown in
	[YAML 1.0][yaml_10_delimiter], [YAML 1.1][yaml_11_delimiter], and [YAML 1.2][yaml_12_delimiter].
* Characters prior to the initiating three dashes (`---`) and after the three periods (`...`) are not allowed.
* Multi-document YAML streams are **not** allowed.
* Comments inside the YAML stream are **not** allowed.
* More precisely, **only** values which are actual YAML properties or values are allowed.

## Communication document encryption

Prior to sending a communication document, the sending node *must* encrypt it according to
the [network encryption](../cryptography/#network-encryption) specifications. A node sending
a communication document without first encrypting is *must* be treated as though its keys
have been compromised.

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

[syml]: http://github.com/sdmp/signed-yaml
[w_iso8601]: https://en.wikipedia.org/wiki/ISO_8601
[yaml_10_delimiter]: http://yaml.org/spec/1.0/#id2489959
[yaml_11_delimiter]: http://yaml.org/spec/1.1/#id857577
[yaml_12_delimiter]: http://www.yaml.org/spec/1.2/spec.html#id2760395
