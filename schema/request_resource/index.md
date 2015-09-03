---
layout: schema
title: request_resource
subtitle: Nodes requesting specific published resources from other nodes.
---


When a user publishes a [resource](/resource/), the resource object may not
be transmitted to all nodes. This may be because the receiving node is not
online, or other technical network-specific limitations. A node may request
a resource from another node using this request object.

---

## Schema: `request_resource` ([JSON Schema][schema])

Used to request a specific resource from another node.

This object contains the following properties:

###### `request` *(object, required)*

Holds the request properties.

###### `request.type` *(string, required)*

Indicates the request type. This must be the string `resource`.

###### `request.identifier` *(string, required)*

Responses to this resource request will include this identifier in the response object. The
string may contain up to 40 ASCII characters (inclusive).

Implementations of the SDMP should attempt to make this property unique per request and per node.

###### `request.author` *(string, required)*

The [key fingerprint](/cryptography/#key-fingerprint) of the *user* who published
the resource being requested.

###### `request.resource` *(string, required)*

The [resource identifier](/resource/#resource-identifier) of the resource being requested.


[schema]: https://github.com/sdmp/sdmp-schema/blob/master/schemas/request_resource.json
