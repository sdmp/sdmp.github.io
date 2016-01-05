---
layout: docs
title: Container
subtitle: JSON object with schemas.
---


Within the SDMP, the "container" is a [JSON object](http://json.org/)
defined using a schema [in this document](#json-schema), which uses the
syntax of the [JSON Schema](http://json-schema.org/).

The container JSON object requires certain properties, but other
applications may extend the container by specifying schemas
to use for validation.

---

## Container Extension

A container object is "extended" by specifying a valid
[JSON Schema](http://json-schema.org/) within the object (the `schemas`
property). The additional JSON Schema specified by the `schema` property
entry is called the "container extension".

Every schema entry must be a valid [SDMP resource URI](/resource#resource-uri)
or one of the strings of the [core schemas](/schema).

---

## Validation

The JSON object is validated using the
[JSON Schema method](http://json-schema.org/latest/json-schema-validation.html)
of object validation, and is validated first against the container schema
(specified at [the end](#json-schema) of this document) and then validated
against any other schemas specified, *in the order* in which they are listed.

If any of the following are true, the container object must be considered invalid:

* The container itself is considered invalid according to the container schema
	specified in this document (see [here](#json-schema)).
* Any entry in the schema array is an invalid [SDMP resource URI](/resource#resource-uri).
* Any schema URI specified within the container schema list cannot be resolved by
	the application validating the container.
* The container is considered invalid according to any schema specified
	within the container.

---

## JSON Properties

The JSON object of the container has the following required properties:

###### `sdmp` *(object, required)*

This root property of the object holds the metadata which describes the
rest of the object.

###### `sdmp.version` *(string, required)*

The version of the SDMP specifications used to define and validate the
properties of the container object.

This property must be a valid [semver](http://semver.org/) number.

Example: `0.10.6`

###### `sdmp.schemas` *(array, required)*

Each array element of this property must be a [UTF-8](http://www.utf-8.com/)
encoded string which must be either:

1. a valid [SDMP resource URI](/resource/#resource-uri), which must
	resolve to a valid [JSON schema](http://json-schema.org/), or
2. one of the following string values, which are schemas core to the SDMP:
	- [identity](/core/identity)
	- [encrypted](/core/encrypted)
	- [signature](/core/signature)
	- [sharedkey](/network/sharedkey)
	- [resource](/journal/resource)
	- [broadcast](/journal/broadcast)
	- [request_journal](/journal/request_journal)
	- [request_resource](/journal/request_resource)
	- [response](/journal/response)
	- [message](/schema/message)
	- [receipt](/schema/receipt)
	- [node](/schema/node)
	- [user](/schema/user)
	- [trust](/schema/trust)
	- [revoke](/schema/revoke)

---

## Example

Consider this example SDMP [resource URI](/resource/#resource-uri):

	sdmp://GlvAreTo0lCSyum7Wzh8pzhxYOOu-gMIgO2N95AAwAGP6-nR8xCvWvIW0t9rF_ZZfpCY_fDV38JDFKaOU91A8Q/h6FWguOHjaB5eUCsjeSIUGxA6p2abtY6HmaUin0F_9INc60VT3IELkP-q7IuWEwBCA8SpIbkfO9ZAIj5jgusYA

If this example URI yields a valid [JSON schema](http://json-schema.org/):

	{
		"type": "object",
		"properties": {
			"hello": {
				"type": "string",
				"pattern": "^[a-z]+$"
			}
		},
		"required": [ "hello" ]
	}

Then an example valid container might look like this:

	{
		"sdmp": {
			"version": "0.10.6",
			"schemas": "sdmp://GlvAreTo0lCSyum7Wzh8pzhxYOOu-gMIgO2N95AAwAGP6-nR8xCvWvIW0t9rF_ZZfpCY_fDV38JDFKaOU91A8Q/h6FWguOHjaB5eUCsjeSIUGxA6p2abtY6HmaUin0F_9INc60VT3IELkP-q7IuWEwBCA8SpIbkfO9ZAIj5jgusYA"
		},
		"hello": "world"
	}

An example **invalid** container might look like this:

	{
		"sdmp": {
			"version": "0.10.6",
			"schemas": "sdmp://GlvAreTo0lCSyum7Wzh8pzhxYOOu-gMIgO2N95AAwAGP6-nR8xCvWvIW0t9rF_ZZfpCY_fDV38JDFKaOU91A8Q/h6FWguOHjaB5eUCsjeSIUGxA6p2abtY6HmaUin0F_9INc60VT3IELkP-q7IuWEwBCA8SpIbkfO9ZAIj5jgusYA"
		},
		"hello": 123
	}

> Note: This is invalid because the schema requires the value of
> the property `hello` to be a *string*.

And another example **invalid** container might look like this:

	{
		"sdmp": {
			"version": "0.10.6",
			"schemas": "sdmp://GlvAreTo0lCSyum7Wzh8pzhxYOOu-gMIgO2N95AAwAGP6-nR8xCvWvIW0t9rF_ZZfpCY_fDV38JDFKaOU91A8Q/h6FWguOHjaB5eUCsjeSIUGxA6p2abtY6HmaUin0F_9INc60VT3IELkP-q7IuWEwBCA8SpIbkfO9ZAIj5jgusYA"
		}
	}

> Note: This is invalid because the schema requires that the value
> of the property `hello` be present.

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
						"type": "string"
					},
					"schemas": {
						"type": "array",
						"items": {
							"type": "string",
							"pattern": "^sdmp:\/\/[a-zA-Z0-9]+\/$"
						},
						"minItems": 1,
						"uniqueItems": true
					}
				},
				"required": [ "version", "schemas" ]
			}
		},
		"required": [ "sdmp" ]
	}
