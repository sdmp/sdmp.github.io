---
title: control
subtitle: Document descriptions for SDMP-specific controls.
layout: page
---


Inside the root [message object of a message](./message) is an optional
property named `control`. This property describes the addition and
updating of users and nodes, the relationships between users, and
the trust level between users and nodes.

The `control` property is an object containing the following fields:

## `control: node`

Used when creating a new node.

Additional fields **required** in the root `message` object are:

* `key` : The binary encoded public key of the node, which is equivalent to
	an unarmored export of an OpenPGP public key.

This message SYML **must** be signed by the node.

Deleting this resource indicates that this node is no longer secure, and
the node **may not** be communicated with.

## `control: node-info`

Used to publish information about a node.

Additional fields **required** in the root `message` object are:

* `network` : An array of strings which are the network addresses for the node.
* `name` : A human-friendly name for the node.

The network addresses **must** be either IPv4 **or** IPv6. Nodes **should**
favor the IPv6 address, where possible.

Network addresses **must** include the port number.

## `control: node-permission`

Used to indicate a users level of trust in a node. This is what authorizes
a node to publish messages on a users behalf, or synchronize messages that
are intended for a user.

Additional fields **required** in the root `message` object are:

* `node` : The [key fingerprint](./cryptography) of the public key of
	the node, encoded as a hexadeciimal string.
* `user` : The [key fingerprint](./cryptography) of the public key of
	the user, encoded as a hexadecimal string.
* `authority` : Currently only `host` and `publisher` are supported.
	- A node given `host` permissions by a user is authorized to *synchronize* (i.e.
		host, similar to an IMAP server) messages for a user.
	- A node given `publisher` permissions by a user is authorized to *create*
		messages on the users behalf (i.e., an application on the users laptop).

This resource **must** be signed using the user's key.

Deleting this resource indicates that the user no longer trusts that node,
therefore messages from this node should **not** be synchronized.

## `control: user`

Used when creating a new user.

Additional fields **required** in the root `message` object are:

* `key` : The binary encoded public key of the user, which is equivalent to
	an unarmored export of an OpenPGP public key.

This message SYML **must** be signed by the created user.

Deleting this resource indicates that the user wishes to delete
*all* documents published by that user.

## `control: user-info`

Used to publish information about a user.

Additional fields **required** in the root `message` object are:

* `info` : Any publicly visible information about the user. **Requires**
	at least the following fields:
	- `name` : The publicly visible name of the user.
	- `about` : A brief description of the user.

This resource **must** be signed by the user **or** by any node
authorized by the user as a `publisher`.

## `control: connection`

Used to indicate a relationship between users.

Additional fields **required** in the root `message` object are:

* `connection` : The [key fingerprint](./cryptography) of the public key 
	of the user being connected, encoded as a hexadeciimal string.

This resource **must** be signed by the user **or** by any node
authorized by the user as a `publisher`.

Deleting this resource indicates that you no longer wish to synchronize
streams from that user.

## `control: connection-request`

This resource notifies others that you want another user to add
you as a connection. This is a convenience method to make it
easier to connect to other users.

Additional fields **required** in the root `message` object are:

* `connection` : The [key fingerprint](./cryptography) of the public key
	of the user being connected to, encoded as a hexadeciimal string.

This resource **must** be signed by the user key **or** by
any node authorized by the user as a `publisher`.
