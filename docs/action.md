---
title: action
subtitle: Descriptions of the CRUD-like actions.
layout: page
---


In the SDMP, synchronizing resources between nodes is a simple
CRUD-like approach. The actions available are `create`, `read`,
`update`, and `delete`.

An [SDMP message](./message) **requires** an `action` property in the
root `message` object. The `action` property is a string with the
following values:

## `action: create`

Used when creating a new resource.

## `action: read`

Used when requesting an existing resource.

> TODO: should it give back the latest, or all? updated vs deleted?

## `action: update`

Used when updating an existing resource.

Since resources are identified by the hash of their content, literally
updating a resource is not allowed. Instead, "updating" a resource
creates a new resource and references the old versions.

The following restrictions apply for an `update` action:

* A resource may only be updated by the user who created the original.
* When a resource is updated, it **must** include the list of **all**
	previous resource versions under the `resources` property.
* The `update` action **replaces** the existing resource, it is not a
	computed difference of the existing data.

## `action: delete`

Used when deleting an existing resource.

In recognition of the fact that published resources have been synchronized
across a number of nodes, it is noted here that the `delete` action does not
*literally* delete a resource. It cannot be cryptographically verified that
a node has actually deleted a resource, and due to the network topology
a resource may be accessible *long* after the `delete` action has been published.

Therefore, the `delete` action should be considered a *request* to other nodes
to not make the resource available.

A properly functioning node will **never** make a resource available once
a `delete` action has been recorded for that resource.

The following restrictions apply for a `delete` action:

* A resource may only be deleted by the user who created the original.
* When a resource is deleted, it **must** include the list of **all**
	previous resource versions under the `resources` property.

## in addition

For the `update` and `delete` actions, if there is not **at least**
`1` value in the `resources` collection, the resource **must not** be
considered valid.

If **any** of the references in the `resources` list are for previous resources
not authored by the author of the `update`/`delete` resource, the resource
**must not** be considered valid.

> #### Note
> Because a resource is synchronized over time to many nodes, it should
> be considered that updating and deleting resources is not instantaneous, and
> due to network topology it may be possible to acquire an older version of a
> resource long after the update or delete action has been issued.
>
> In fact, there cannot be a secure guarantee that an old version of a
> resource will ever be completely removed.
