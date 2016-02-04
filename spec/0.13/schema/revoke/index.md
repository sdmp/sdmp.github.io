---
version: 0.13
parent: schema
layout: spec
title: revoke
subtitle: Revoke a published resource.
---


A revocation is a resource which revokes a previously published
resource. Revoked resources replace existing resources. When a
resource is requested, if the resource has been revoked the proper
response is the revocation resource.

---

## Limitations

Since this protocol is a synchronized system and does not use
a central authority, please note that revoking a resource is
not an instantaneous action--revoking a resource may take a
very long time, and in fact there is no guarantee that the
resource will ever be fully removed from the network.

---

## Revoke an Identity

An identity may publish a revocation of its own identity
resource. Doing so indicates that the private key of the
identity should be considered invalid after the the
revocation has been published. Any data originating from
the identity after the revocation should be considered
invalid.

It is left as an implementation decision whether data
prior to the revocation should be treated as invalid
after the revocation is published.

---

## Description

This object contains the following property:

###### `revoke` *(object, required)*

Holds the properties used to revoke the resource.

This contains the following reserved properties:

###### `revoke.resource` *(string, required)*

The [resource identifier](../../journal/resource#resource-identifier)
of the [resource](../../journal/resource) being revoked.

###### `revoke.authorization` *(string, optional)*

If the revocation is being published by an identity other than
the identity used to publish the original resource, this value
must be present.

This value is the [resource identifier](../../journal/resource#resource-identifier)
of the [trust](../../schema/trust) resource giving this identity
authorization to revoke the resource.

---

## Schema

	{
	  "$schema": "http://json-schema.org/draft-04/schema#",
	  "type": "object",
	  "properties": {
	    "revoke": {
	      "type": "object",
	      "properties": {
	        "resource": {
	          "type": "string"
	        },
	        "authorization": {
	          "type": "string"
	        }
	      },
	      "required": [ "resource" ]
	    }
	  },
	  "required": [ "revoke" ]
	}
