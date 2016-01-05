---
layout: docs
title: Schema
subtitle: Schemas for container extensions which are part of the SDMP core.
---


Certain container types core to the SDMP are described here.

## [Create Trust](./trust)

A trust is a [resource](/journal/resource) which authorizes
another user or node to take particular actions on behalf of
the identity signing the resource.

## [Revoke Resource](./revoke)

Revoke a resource by publishing another resource referencing the
original resource.

## [User Information](./user)

Information about the user is published separately from the user
[identity resource](/core/identity). This object contains information
about the user, such as a name or other communication channels, like email.

## [Node Information](./node)

Information about the node is published separately from the node
[identity resource](/core/identity). This object contains information
about the node, such as an IP address and port, or other protocol
details necessary for connecting to the node.

## [Message](./message)

This object defines simple human-readable messages.

## [Resource Receipt](./receipt)

When sending a resource across the network, this schema offers a secure
way to let the sender know the message was received.
