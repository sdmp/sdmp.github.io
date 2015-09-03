---
layout: schema
title: response
subtitle: Response to a received request object.
---


When a node requests a specific resource or a journal list update, the
response is returned inside a response object.

---

## Schema: `response` ([JSON Schema][schema])

Used when a node responds to a [resource request](/schema/request_resource/) or
a [journal list request](/schema/request_journal/).

This object contains the following properties:

###### `response` *(object, required)*

Holds the data properties. This contains the following reserved properties:

###### `response.description` *(string, optional)*

An optional string to hold a human-readable description of the data object. The string
may contain only up to 40 ASCII characters. Primarily useful for debugging purposes.

###### `response.status` *(number)*

This property must be a valid status code. (These are similar to HTTP status codes, in
that they indicates the status of the response.) Valid status codes are:

* `200` **"OK"** - Used to indicate a response object containing a valid resource.
* `400` **"Bad request"** - Used to indicate that a received request was not interpretable.
* `404` **"Not found"** - This status is used in all cases where a requested resource
  is unavailable or when the resource has been deleted.

Note: If you want additional status codes for your application and want more widespread
adoption, please make a request by [filing an "issue" on Github][issue] and your custom
status code may make it into the specs!

###### `response.request` *(string)*

This property must be the exact value of the `request.identifier` found in the
request object.

###### `response.data` *(object)*

If the object contains a valid [resource object](/resource/), or if it contains a
valid [journal list](/journal/), this object is the [signed SDMP container](TODO).
In all other cases, this property must not be set.


[w_forward]: https://en.wikipedia.org/wiki/Forward_secrecy
[schema]: https://github.com/sdmp/sdmp-schema/blob/master/schemas/response.json
[issue]: https://github.com/sdmp/sdmp.github.io/issues
