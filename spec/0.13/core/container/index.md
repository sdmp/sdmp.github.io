---
version: 0.13
parent: core
layout: spec
title: Container
subtitle: JSON object with schemas.
---


Within the SDMP, the "container" is a [JSON object](http://json.org/) which
contains metadata describing itself programmatically. The JSON object is
defined using the [JSON Schema](http://json-schema.org/) syntax.
Applications extend the container by specifying JSON schemas.

---

## Reserved Property

The container JSON object reserves the property `sdmp` for
use by the specification alone.

---

## Container Extension

A container object is "extended" by specifying a valid
[JSON Schema](http://json-schema.org/) within the `sdmp.schemas`
list. The schema must be specified using either a
[resource URI](../resource#resource-uri) or the name
of any core schema.

---

## Container Extension Data

The data available to the extension must be placed in
the container as a property using the name in the
`sdmp.schemas` list.

For example, if the extension is a core schema of the name
`identity`, the data for that extension would be under the
property named `identity`, at the root of the container
JSON object.

---

## Core Schema Names

These schemas can be referenced in the `sdmp.schemas` property
by name directly. Where used, they must be interpreted to mean
the core schema of the version specified by the `sdmp.version`
property.

* [identity](../identity)
* [encrypted](../encrypted)
* [signature](../signature)
* [resource](../resource)
* [shared_key](../../network/shared_key) <!-- should this be part of core? -->
* [broadcast](../../journal/broadcast)
* [request_journal](../../journal/request_journal)
* [request_resource](../../journal/request_resource)
* [response](../../journal/response)
* [message](../../schema/message)
* [receipt](../../schema/receipt)
* [node](../../schema/node)
* [user](../../schema/user)
* [trust](../../schema/trust)
* [revoke](../../schema/revoke)

---

## Validation

The JSON object is validated using the
[JSON Schema method](http://json-schema.org/latest/json-schema-validation.html)
of object validation.

The container JSON object is first validated against the
[container schema](#json-schema), and then each named property
is validated using the schema specified by the property name
in the order in which they are listed in the `sdmp.schemas`
list property.

If any of the following are true, the container object must be considered invalid:

* The container itself is considered invalid according to the container
  schema specified [in this document](#json-schema).
* Any entry in the schema array is an invalid
  [SDMP resource URI](../resource#resource-uri).
* Any schema URI specified within the container schema list cannot be
  resolved by the application validating the container.
* The container object does not have a property with the same name
  as the specified URI.
* For any schema listed, the corresponding container property is
  considered invalid according to its schema.

---

## JSON Properties

The JSON object of the container has the following required properties:

###### `sdmp` *(object, required)*

This root property of the object holds the metadata which describes the
rest of the object.

###### `sdmp.version` *(string, required)*

The version of the SDMP specifications used to define and validate the
properties of the container object.

This property must be a valid [semver](http://semver.org/) number
containing only the major and minor version.

Example: `0.13`

###### `sdmp.schemas` *(array, required)*

Each array element of this property must be a [UTF-8](http://www.utf-8.com/)
encoded string which must be either:

1. a valid [SDMP resource URI](../../journal/resource#resource-uri), which must
	resolve to a valid [JSON schema](http://json-schema.org/), or
2. one of the string values of the [core schema names](#core-schema-names).

---

## Example

Consider this example SDMP [resource URI](../resource/#resource-uri)
(shortened with ellipsis for readability):

	sdmp://GlvA...1A8Q/h6FW...usYA

If this example URI yields a valid [JSON schema](http://json-schema.org/):

	{
		"type": "object",
		"properties": {
			"hello": {
				"type": "string",
				"pattern": "^[a-z]+$"
			}
		}
	}

Then an example valid container might look like this (URI and property
name shortened with ellipsis for readability):

	{
		"sdmp": {
			"version": "0.10.6",
			"schemas": [
				"sdmp://GlvA...1A8Q/h6FW...usYA"
			]
		},
		"sdmp://GlvA...1A8Q/h6FW...usYA": {
			"hello": "world"
		}
	}

An example **invalid** container might look like this:

	{
		"sdmp": {
			"version": "0.10.6",
			"schemas": [
				"sdmp://GlvA...1A8Q/h6FW...usYA"
			]
		},
		"sdmp://GlvA...1A8Q/h6FW...usYA": {
			"hello": 123
		}
	}

> Note: This is invalid because the schema requires the value of
> the property `hello` to be a *string*.

Another example **invalid** container might look like this:

	{
		"sdmp": {
			"version": "0.10.6",
			"schemas": [
				"sdmp://GlvA...1A8Q/h6FW...usYA"
			]
		}
	}

> Note: This is invalid because the container object must contain
> a property for the specified schema.

And another example **invalid** container might look like this:

	{
		"sdmp": {
			"version": "0.10.6",
			"schemas": []
		},
		"sdmp://GlvA...1A8Q/h6FW...usYA": {
			"hello": "world"
		}
	}

> Note: This is invalid because all container properties other
> than `sdmp` must be listed in the `sdmp.schemas` list.

---

## JSON Schema

The exact [JSON Schema](http://json-schema.org/) for the container object is:

	{
		"$schema": "http://json-schema.org/draft-04/schema#",
		"type": "object",
		"properties": {
			"sdmp": {
				"type": "object",
				"properties": {
					"version": {
						"type": "string",
						"pattern": "^\\d+\\.\\d+$"
					},
					"schemas": {
						"type": "array",
						"items": {
							"type": "string"
						},
						"uniqueItems": true
					}
				},
				"required": [ "version", "schemas" ]
			}
		},
		"required": [ "sdmp" ]
	}
