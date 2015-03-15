---
title: response
subtitle: The proper response to a message.
layout: page
---


The response of a node to a [message](./message) is a response message. This message
is either the requested message of a `read` [action](./action), or a status response
for any other action.

## Response format

All responses **must** be valid [SDMP messages](./messages). The message **must**
contain the object named `response` on the `message` object, and contains the following
properties:

### `status` (integer, optionally required)

Based on [HTTP status codes](httpcodes), the `status` property is one of the following values:

* `102` (Processing) - For certain [actions](./action), the responding node may find it
	beneficial to tell the messaging node that the action will take some time. If so, the
	responding node would use this `102` status, and **should** include a `delay` value.
* `200` (Okay) - Used when the action does not require an object response (e.g. everything
	except `read`), and the responding node is satisfied with the message.
* `201` (Created/Updated) - Used when the action is either `create` or `update`. Indicates
	that the responding node has created/updated that message.
* `202` (Received) - Given Similar to the HTTP status, this response indicates that the message has
	been *received*, but does not indicate that the responding node has taken action.
* `301` (Updated permanently) - When a message has been *updated* and a node requests an
	old version of that resource, this status is given. When this status is given, the
	`redirect` property of the `response` object **must** be set.
* `304` (Not modified) - When a node requests a message and includes the `have_version`
	property on the `message` object, if the responding node does not have a newer version
	this response status is used.
* `400` (Bad request) - The responding node cannot or will not process the message due to
	something that is perceived to be a client error, such as malformed message syntax, or
	when the messaging node is not authorized for the message action.
* `404` (Not found) - Used in response to `read` actions, when the resource is not available.
* `409` (Conflict) - Indicates that the `update` or `delete` action could not be processed
	because of version conflicts. Typically used when not all previous resources are included.

### `redirect` (string, optional)

When the status indicates a resource has been updated (e.g. status `301`) this property
is required. The `redirect` property is the [URI](./uri-scheme) of the most recent
version of that resource.

### `delay` (integer, optional)

If the response `status` is `102`, the responding node **may** give a delay in
seconds. The messaging node **should** respect this delay, and wait for the specified
number of seconds to make the same request.

[httpcodes]: https://en.wikipedia.org/wiki/List_of_HTTP_status_codes
