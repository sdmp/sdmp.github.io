---
layout: schema
title: resource
subtitle: Published objects are called resources.
---


All resources published in the SDMP are valid [containers](/schema/container/),
and require this resource schema as the **first** schema in the container
object `sdmp.schemas` property.

---

## Schema: `resource` ([JSON Schema][schema])

Contains metadata about the resource. All resources published in the SDMP contain
the following additional properties:

###### `sdmp.created` *(string, required)*

Timestamp the container was created.

The time must be the local time of the one creating the container, translated to UTC,
and formatted using [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601), with granularity
to the millisecond. E.g., `2015-07-26T15:48:37.703Z`.

###### `sdmp.publisher` *(string, required)*

The [key fingerprint](/cryptography/#key-fingerprint) of the user or node that generated
and signed the container.

###### `sdmp.updates` *(array of strings, optional)*

Used to indicate a revocation of a previously published resource. The previous
resource(s) are to be replaced with this resource. Each array element is the
[resource identifier](/resource/#resource-identifier) of the resource to be replaced.

Note: Deleting a resource is done by creating a new resource with no data, which
contains the resources to be deleted in the `sdmp.updates` property.


[resource]: https://github.com/sdmp/sdmp-schema/blob/master/schemas/resource.json
