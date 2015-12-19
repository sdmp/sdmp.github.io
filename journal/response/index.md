---
layout: docs
title: response
subtitle: Response to a received request object.
---

When a node requests a [resource](/journal/request_resource) or a
[journal update](/journal/request_journal), the response is returned
inside a response object.

---

## Response Object

When a response is transmitted, it must be sent inside of a valid
[resource](/journal/resource) object, which must be signed by the
node sending the resource.

---

## Description

Used when a node responds to a [resource request](/journal/request_resource)
or a [journal update request](/journal/request_journal).

This object contains the following properties:

###### `response` *(object, required)*

Holds the data properties. This contains the following reserved properties:

###### `response.status` *(string, required)*

This property must be a valid status string which indicates the status
of the response. Valid status strings are:

* `ok` - Used to indicate a response object containing a valid object.
* `bad_request` - Used to indicate that a received request could not
	be interpreted by the receiver.
* `not_found` - This status is used in all cases where a request is
  unavailable, for any reason.

> Note: If you want additional status strings for your application
> and want more widespread adoption, please make a request by filing
> an [issue](https://github.com/sdmp/sdmp.github.io/issues) on Github
> and your custom status code may make it into the specs!

###### `response.identifier` *(string, required)*

This property *must* be the exact value of the `request.identifier` found
in the [request object](/journal/request_resource).

###### `response.data` *(object)*

If the value `response.status` is `ok`, this object must be the requested
[resource object](/journal/resource) or the requested [journal update](/journal/broadcast).

In all other cases, this property must not be set.

---

## Schema

	{
	  "$schema": "http://json-schema.org/draft-04/schema#",
	  "type": "object",
	  "properties": {
	    "response": {
	      "type": "object",
	      "properties": {
	        "status": {
	          "type": "string",
	          "pattern": "^(ok|bad_request|not_found)$"
	        },
	        "identifier": {
	          "type": "string"
	        },
	        "data": {
	          "type": "object"
	        }
	      },
	      "required": [ "status", "identifier" ]
	    }
	  },
	  "required": [ "response" ]
	}
