---
layout: schema
title: request_journal
subtitle: Requesting blocks of journal entries.
---


[Synchronization](/synchronize/) between nodes is done by transmitting
[journal lists](/journal/). However, if a node is offline or has other
reason to suspect a desynchronization has occured, the node may request
the journal entries of another node.

---

## Schema: `request_journal` ([JSON Schema][schema])

Nodes may request journal entries from other nodes. This may be done if a node
has been offline for a while, or other technical reasons.

This object contains the following properties:

###### `request` *(object, required)*

Holds the request properties.

###### `request.identifier` *(string, required)*

Responses to this resource request will include this identifier in the response object. The
string may contain up to 40 ASCII characters (inclusive).

Implementations of the SDMP should attempt to make this property unique per request and per node.

###### `request.type` *(string, required)*

Indicates the request type. This must be the string `journal`.

###### `request.since` *(string, required)*

This property is the *last known* [journal line identifier](/journal/#journal-line-identifier) of
the requesting node. A successful response to this request would have the journal entries
immediately after this entry, and would not include this line identifier.

###### `request.limit` *(integer, optional)*

The *maximum* number of journal line identifier entries to include in the response. The
responding node may not exceed this limit, but if this value is not defined in the
request, the responding node may choose whatever limit it decides is appropriate.


[schema]: https://github.com/sdmp/sdmp-schema/blob/master/schemas/request_journal.json
