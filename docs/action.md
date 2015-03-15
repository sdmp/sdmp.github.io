---
title: action
subtitle: Document requirements for CRUD-like actions.
layout: page
---


In the SDMP, synchronizing resources between *nodes* takes a simple
CRUD-like approach. The actions available are `create`, `read`,
`update`, and `delete`.

Any [SDMP resource](./resource) may have an action defined in the manifest
file. To do this, the field `action` is set to `create`, `read`, `update`,
or `delete`.

## `"action": "create"`

Used when creating a new resource.

## `"action": "read"`

Used when requesting a resource.

## `"action": "update"`

Used when updating an existing resource.

Any resource posted to another *node* may be updated by the *user*
who originally posted the resource.

The new resource **must** reference all previous resources which are
being updated.

This requires the field `hashes` in the manifest, which is a list of
the resource identifiers of **all** previous resources.

The `update` action **replaces** the existing resource, it is not a
computed difference of the existing data.

## `"action": "delete"`

Used when deleting an existing resource.

Any resource posted to another *node* may be deleted by the *user*
who originally posted the resource.

The delete resource **must** reference all previous resources which are
being updated.

This requires the field `hashes` in the manifest, which is a list of
the resource identifiers of **all** previous resources.

## in addition

For the `update` and `delete` actions, if there is not **at least**
`1` resource hash in the `hashes`, the resource **must not** be
considered valid.

If **any** of the hashes in the `hashes` list are for previous resources
not authored by the `update`/`delete` resource author, the resource
**must not** be considered valid.

> #### Note
> Because a resource is synchronized over time to many *nodes*, it should
> be considered that updating and deleting resources is not instantaneous, and
> due to network topology it may be possible to acquire an older version of a
> resource long after the update or delete action has been issued.
>
> In fact, there cannot be a secure guarantee that an old version of a
> resource will ever be completely removed.
