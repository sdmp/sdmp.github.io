---
layout: schema
title: container
subtitle: Used to hold any type of data, both published resources and network communication data.
---


The SDMP makes use of JSON objects which are defined using [JSON Schema](http://json-schema.org/).
The most important JSON object is the *container* object, which holds the schema validation
information.

---

## Container Validation

The container object is first validated using this container schema, after which
all schemas referenced in the `sdmp.schemas` object are used to validate the
container object, in the order they are found in the array.

If any schema is invalid, the container object must be considered completely invalid.

---

## Schema: `container` ([JSON Schema][schema])

Base object which contains all other information.

This object contains the following properties:

###### `sdmp` *(object, required)*

The root property of the object, which describes the rest of the object.

###### `sdmp.version` *(string, required)*

The version of the SDMP specifications used to define and validate the
properties of the container object.

This property must be a valid [semver number](http://semver.org/).

###### `sdmp.schemas` *(array of strings, required)*

Each array element of this property must be *either* a valid
[URI](https://tools.ietf.org/html/rfc3986), which should resolve to a valid
[JSON schema file](http://json-schema.org/), *or* one of the following exact
reserved strings:

* `connection`
  ([schema](https://github.com/sdmp/sdmp-schema/blob/master/schemas/connection.json),
  [specs](/schema/connection))
* `encrypted`
  ([schema](https://github.com/sdmp/sdmp-schema/blob/master/schemas/encrypted.json),
  [specs](/schema/encrypted))
* `identity`
  ([schema](https://github.com/sdmp/sdmp-schema/blob/master/schemas/identity.json),
  [specs](/schema/identity))
* `information_node`
  ([schema](https://github.com/sdmp/sdmp-schema/blob/master/schemas/information_node.json),
  [specs](/schema/information_node))
* `information_user`
  ([schema](https://github.com/sdmp/sdmp-schema/blob/master/schemas/information_user.json),
  [specs](/schema/information_user))
* `journal`
  ([schema](https://github.com/sdmp/sdmp-schema/blob/master/schemas/journal.json),
  [specs](/schema/journal))
* `message`
  ([schema](https://github.com/sdmp/sdmp-schema/blob/master/schemas/message.json),
  [specs](/schema/message))
* `receipt`
  ([schema](https://github.com/sdmp/sdmp-schema/blob/master/schemas/receipt.json),
  [specs](/schema/receipt))
* `relationship`
  ([schema](https://github.com/sdmp/sdmp-schema/blob/master/schemas/relationship.json),
  [specs](/schema/relationship))
* `request_journal`
  ([schema](https://github.com/sdmp/sdmp-schema/blob/master/schemas/request_journal.json),
  [specs](/schema/request_journal))
* `request_resource`
  ([schema](https://github.com/sdmp/sdmp-schema/blob/master/schemas/request_resource.json),
  [specs](/schema/request_resource))
* `resource`
  ([schema](https://github.com/sdmp/sdmp-schema/blob/master/schemas/resource.json),
  [specs](/schema/resource))
* `response`
  ([schema](https://github.com/sdmp/sdmp-schema/blob/master/schemas/response.json),
  [specs](/schema/response))

[schema]: https://github.com/sdmp/sdmp-schema/blob/master/schemas/container.json
