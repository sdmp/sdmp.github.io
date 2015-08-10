---
layout: docs
title: Resource
subtitle: Resources passed between nodes and how to interpret them.
---


All resources published in the SDMP are valid [SDMP containers](../container/),
and require the [resource](#resource) schema as the *first* schema in the payload
container object `sdmp.schemas` property.

---

## Core Resource Types

The following schemas are considered core to the SDMP, and should be supported by
all implementations:

* **[Resource](#resource) ([schema][schema_resource]):** All published resources
  in the SDMP must conform to this core schema.
* **[Identity](#identity) ([schema][schema_identity]):** Used to create a user or node.
* **[Node Information](#node-information) ([schema][schema_node_information]):** Used
  to publish information about a node, particularly the IP address and port used to connect.
* **[User Information](#user-information) ([schema][schema_user_information]):** Used
  to publish information about a user, such as the name.
* **[Relationship](#relationship) ([schema][schema_relationship]):** Indicates relationships
  between users, and between users and nodes.
* **[Post](#post) ([schema][schema_post]):** A simple schema used to publish public posts
  using [markdown](http://commonmark.org/).
* **[Message](#message) ([schema][schema_message]):** Used to send message to another user.
* **[Message Receipt](#message-receipt) ([schema][schema_message_receipt]):** Used to confirm
  delivery of a message.
* **[Encrypted](#encrypted) ([schema][schema_encrypted]):** Stores information in a strongly
  secure manner. The encrypted data must also be a valid [SDMP container](../container/).

---

## Resource Identifier

The SDMP [requires SHA-512](../cryptography/#hashing) as the signing algorithm. Using the terminology of the
[JSON Web Signature (JWS)](http://self-issued.info/docs/draft-ietf-jose-json-web-signature.html)
specifications, the *resource identifier* is the SHA-512 output of the *JWS Signing Input*, whose
octets are [unpadded base64url][base64] encoded.

## Resource Identifier Example

Suppose that the following is a sample resource:

```
{
  "sdmp": {
    "version": "0.10.1",
    "publisher": "GlvAreTo0lCSyum7Wzh8pzhxYOOu-gMIgO2N95AAwAGP6-nR8xCvWvIW0t9rF_ZZfpCY_fDV38JDFKaOU91A8Q",
    "created": "2015-06-19T23:15:52.605Z",
    "schemas": [ "resource", "post" ]
  },
  "post": {
    "title": "Life is Great",
    "content": "When you are **awesome**!\n\nCheck out my [things](http://something.com)!"
  }
}
```

Calling `JSON.stringify` on this object would yield the following (newlines
added only for readability):

```
{"sdmp":{"version":"0.10.1","publisher":"GlvAreTo0lCSyum7Wz
h8pzhxYOOu-gMIgO2N95AAwAGP6-nR8xCvWvIW0t9rF_ZZfpCY_fDV38JDF
KaOU91A8Q","created":"2015-06-19T23:15:52.605Z","schemas":[
"resource","post"]},"post":{"title":"Life is Great","content":
"When you are **awesome**!\n\nCheck out my [things](http://something.com)!"
}}
```

This string would be used as the *JWS payload* of the JWS object. (Note that the payload
is for the *string*, not for the JSON object.)

The [JWS example](http://self-issued.info/docs/draft-ietf-jose-json-web-signature.html#rfc.appendix.A.4)
creates the JWS Signing Input string using the following algorithm:

```
BASE64URL(UTF8(JWS Protected Header)) || '.' || BASE64URL(JWS Payload) 
```

For the above [JWS Payload example](#jws-payload), given the same protected header
as in the JWS example, the JWS Signing Input string would be the following
(newlines added only for readability):

```
eyJhbGciOiJFUzUxMiJ9.eyJzZG1wIjp7InZlcnNpb24iOiIwLjEwLjEiLC
JwdWJsaXNoZXIiOiJHbHZBcmVUbzBsQ1N5dW03V3poOHB6aHhZT091LWdNS
WdPMk45NUFBd0FHUDYtblI4eEN2V3ZJVzB0OXJGX1paZnBDWV9mRFYzOEpE
RkthT1U5MUE4USIsImNyZWF0ZWQiOiIyMDE1LTA2LTE5VDIzOjE1OjUyLjY
wNVoiLCJzY2hlbWFzIjpbInJlc291cmNlIiwicG9zdCJdfSwicG9zdCI6ey
J0aXRsZSI6IkxpZmUgaXMgR3JlYXQiLCJjb250ZW50IjoiV2hlbiB5b3UgY
XJlICoqYXdlc29tZSoqIQoKQ2hlY2sgb3V0IG15IFt0aGluZ3NdKGh0dHA6
Ly9zb21ldGhpbmcuY29tKSEifX0
```

And the *resource identifier* of this object would be the following string:

```
OTA8fSUKIIHP4aaJolSzhd9BXLn0hIxXQktpwqdXDDJ56akD5Ok57dILlPgCdjLD4bMnzFRAWHPKy_wgofnLEA
```

---

## URI: Uniform Resource Identifier

All resources published within the SDMP may be referenced using a
[URI](https://tools.ietf.org/html/rfc3986) where only the *scheme name*, *host*,
and *path* are allowed:

* The *scheme name* is `sdmp`.
* The *host* is the [key fingerprint](../cryptography/#key-fingerprint) of
  the *user* who published the resource.
* The *path* is the *resource identifier* of the resource.

E.g., a URI which looks like (newlines added for readability only):

```
sdmp://GlvAreTo0lCSyum7Wzh8pzhxYOOu-gMIgO2N95AAwAGP6-nR8xCvWvIW0t9rF_
ZZfpCY_fDV38JDFKaOU91A8Q/h6FWguOHjaB5eUCsjeSIUGxA6p2abtY6HmaUin0F_9IN
c60VT3IELkP-q7IuWEwBCA8SpIbkfO9ZAIj5jgusYA
```

Would have the following parts:

* *schema name*: `sdmp`
* *host*: `GlvAreTo0lCSyum7Wzh8pzhxYOOu-gMIgO2N95AAwAGP6-nR8xCvWvIW0t9rF_ZZfpCY_fDV38JDFKaOU91A8Q`
* *path*: `h6FWguOHjaB5eUCsjeSIUGxA6p2abtY6HmaUin0F_9INc60VT3IELkP-q7IuWEwBCA8SpIbkfO9ZAIj5jgusYA`

---

## Interpreting URI References

References of the form `sdmp://<HOST>/<PATH>` should be interpreted to
be a reference to the resource published by the user identified by `<HOST>`
and having a resource identifier of `<PATH>`.

References of the form `sdmp://<HOST>` should be interpreted to be a
reference to the [identity resource](#identity) of the user identified by
the `<HOST>` value.

These are equivalent to a request of the form `sdmp://<HOST>/<PATH>`
where `<PATH>` is the resource identifier of the identity resource.

E.g., if `h6FWg...jgusYA` references the [identity resource](#identity)
for the user `GlvAre...U91A8Q`, the following URIs would be equivalent:

```
sdmp://GlvAre...U91A8Q/
sdmp://GlvAre...U91A8Q/h6FWg...jgusYA
```

---

### Resource ([schema: `resource`][schema_resource])

Contains metadata about the resource. All resources published in the SDMP contain
the following additional properties:

###### `sdmp.updates` *(array of strings, optional)*

Used to indicate a revocation of a previously published resource. The previous
resource(s) are to be replaced with this resource. Each array element is the
[resource identifier](#resource-identifier) of the resource to be replaced.

Deleting a resource is done by creating a new resource with no data, which
contains the resources to be deleted in the `sdmp.updates` property.

---

### Resource Examples

A resource containing no additional data would look something like:

```
{
  "sdmp": {
    "version": "0.10.1",
    "publisher": "GlvAreTo0lCSyum7Wzh8pzhxYOOu-gMIgO2N95AAwAGP6-nR8xCvWvIW0t9rF_ZZfpCY_fDV38JDFKaOU91A8Q",
    "created": "2015-06-19T23:15:52.605Z",
    "schemas": [ "resource" ]
  }
}
```

A resource containing two updates and no additional data would look something like:

```
{
  "sdmp": {
    "version": "0.10.1",
    "publisher": "GlvAreTo0lCSyum7Wzh8pzhxYOOu-gMIgO2N95AAwAGP6-nR8xCvWvIW0t9rF_ZZfpCY_fDV38JDFKaOU91A8Q",
    "created": "2015-06-19T23:15:52.605Z",
    "schemas": [ "resource" ],
    "updates": [
      "aIN9oou0wtodzMMKwOSm995WVHFgAHzpO3BnMR4mArwCyIthAXcuTpkbh4ujqTuQRt2_bGlvVHh2wtCSMj-zOQ",
      "zlfYvJkER8fsNVVwQHVtsqn_fNq1ORHzx5lbxr81cs2oyU-lN4nlI6aA3pkhwGf2cX55Qm30Zxhfx6bb7EstVw"
    ]
  }
}
```

---

### Identity ([schema: `identity`][schema_identity])

This resource holds the public key of a user or node, establishing their identity.

Identity resources reserve the following properties:

###### `identity` *(object)*

Holds the identity properties. This contains the following reserved properties:

###### `identity.type` *(string, required)*

Defines the type of identity. The two supported types are `user` and `node`.

###### `identity.expires` *(string, required)*

After this timestamp, the identity must not be considered valid. The time must be UTC. The
timestamp is [ISO 8601][w_iso8601] formatted, with granularity to the millisecond.
E.g., `2015-07-26T15:48:37.703Z`.

Note: An application should not use this key after the date specified, and should not consider
resources signed with this key to be valid if they are believed to be generated after this date.

###### `identity.key` *(string, required)*

The public key of a [globally](https://en.wikipedia.org/wiki/Earth) unique
[key-pair](../cryptography/#key-fingerprint), whose octets are encoded
as [unpadded base64url](https://tools.ietf.org/html/rfc4648#section-5).

---

### Identity Examples

A resource published containing a user identity would look something like this (the key data
has been truncated with ellipsis):

```
{
  "sdmp": {
    "version": "0.10.1",
    "publisher": "GlvAreTo0lCSyum7Wzh8pzhxYOOu-gMIgO2N95AAwAGP6-nR8xCvWvIW0t9rF_ZZfpCY_fDV38JDFKaOU91A8Q",
    "created": "2015-06-19T23:15:52.605Z",
    "schemas": [ "resource", identity" ]
  },
  "identity": {
    "type": "user",
    "expires": "2020-06-19T23:15:52.605Z",
    "key": "AreGlvTo...aOA8QU91"
  }
}
```

A resource published containing a node identity would look identical, except that `identity.type`
would be the string `node` instead.

---

### Node Information ([schema: `node_information`][schema_node_information])

Information about the node is *not* published in the [identity](#identity) resource, it is
published in this object. Primarily, this information holds the IP and port used to connect
to the node.

Node information resources reserve the following properties:

###### `node` *(object)*

Contains information about the node.

###### `node.name` *(string)*

A human-readable description of the node.

###### `node.ips` *(array of strings, required)*

Each array element is a string representation of the IP address *and* port at which the
node is reachable. This address may be IPv4 or IPv6, and the port is **required**.

Nodes *should* prefer the use of IPv6.

---

### Node Information Example

A resource published containing node information might look like this:

```
{
  "sdmp": {
    "version": "0.10.1",
    "publisher": "0lCSyum7GlvAreToWzh8pzhxgO2N95AAYOOu-gMIwAGP6-nR0t9rF_ZZ8xCvWvIWfpCY_fDVU91A8Q38JDFKaO",
    "created": "2015-06-19T23:15:52.605Z",
    "schemas": [ "resource", "node_information"]
  },
  "node": {
    "name": "John's Ubuntu Box",
    "ips": [
      "192.168.2.104:1337",
      "[2607:f298:1:126::72c:b051]:36075"
    ]
  }
}
```

---

### User Information ([schema: `user_information`][schema_user_information])

Information about the user is *not* published in the [identity](#identity) resource, it is
published in this object.

User information resources reserve the following properties:

###### `user` *(object)*

Contains information about the user.

###### `user.name` *(string)*

A human-readable name for the user.

###### `user.about` *(string)*

A human-readable description of the user. Meant to assiste users in differentiating between
known users with similar names.

---

### User Information Example

A resource published containing user information might look like this:

```
{
  "sdmp": {
    "version": "0.10.1",
    "publisher": "GlvAreTo0lCSyum7Wzh8pzhxYOOu-gMIgO2N95AAwAGP6-nR8xCvWvIW0t9rF_ZZfpCY_fDV38JDFKaOU91A8Q",
    "created": "2015-06-19T23:15:52.605Z",
    "schemas": [ "resource", "user_information"]
  },
  "user": {
    "name": "John Doe",
    "about": "Lumberjack and is okay."
  }
}
```

---

### Relationship ([schema: `relationship`][schema_relationship])

Indicating a relationship between two users, or between a node and a user, is a core aspect
of the protocol. There are three defined relationship types:

* **`connection` (user to user):** A user indicating that another user is known to them. This
	indicates to other nodes that resources between those users may be [synchronized](../synchronization/).
* **`publisher` (user to node):** A user indicating that the node may publish resources on
	behalf of the user. Typical use is that a user authorizes a node as a publisher when
	that node is running on a computer operated by that user, such as a laptop or smartphone.
* **`host` (user to node):** A user indicates that the node may send and receive resources
	for the user, but may *not* publish new resources. This is similar to resources being
	[synchronized](../synchronization/) between users.

Relationship resources reserve the following properties:

###### `relationship` *(object)*

Used to store information about the relationship.

###### `relationship.type` *(string, required)*

Defines the type of relationship. Must be one of the following: `connection`, `publisher`, `host`.

###### `relationship.identity` *(string, required)*

The [key fingerprint](../cryptography/#key-fingerprint) of the user or node to which the
relationship is being created. This is equivalent to the SHA-512 hash of the public key,
whose octets are [unpadded base64url](https://tools.ietf.org/html/rfc4648#section-5) encoded.

---

### Relationship Example

A resource published containing a user-to-user relationship information might look like this:

{
  "sdmp": {
    "version": "0.10.1",
    "publisher": "GlvAreTo0lCSyum7Wzh8pzhxYOOu-gMIgO2N95AAwAGP6-nR8xCvWvIW0t9rF_ZZfpCY_fDV38JDFKaOU91A8Q",
    "created": "2015-06-19T23:15:52.605Z",
    "schemas": [ "resource", "relationship" ]
  },
  "relationship": {
    "type": "connection",
    "identity": "0lCSyum7GlvAreToWzh8pzhxgO2N95AAYOOu-gMIwAGP6-nR0t9rF_ZZ8xCvWvIWfpCY_fDVU91A8Q38JDFKaO"
  }
}

A resource published containing a different relationship type would look identical, except
that `relationship.type` would be the string `publisher` or `host` instead.

---

### Post ([schema: `post`][schema_post])

For convenience, the SDMP defines a simple "post" schema, which is intended to be
used for things such as blog posts, tweets, and the like.

Post resources reserve the following properties:

###### `post` *(object, required)*

Used to store information about the post.

###### `post.title` *(string)*

The title of the post.

###### `post.subtitle` *(string)*

The subtitle of the post.

###### `post.content` *(string, required)*

The post content, which *must* be formatted as [markdown](http://commonmark.org/).

---

### Post Example

A resource published containing a post might look like this:

```
{
  "sdmp": {
    "version": "0.10.1",
    "publisher": "GlvAreTo0lCSyum7Wzh8pzhxYOOu-gMIgO2N95AAwAGP6-nR8xCvWvIW0t9rF_ZZfpCY_fDV38JDFKaOU91A8Q",
    "created": "2015-06-19T23:15:52.605Z",
    "schemas": [ "resource", "post" ]
  },
  "post": {
    "title": "Life is Great",
    "content": "When you are **awesome**!\n\nCheck out my [things](http://something.com)!"
  }
}
```

---

### Message ([schema: `message`][schema_message])

For convenience, the SDMP defines a simple "message" schema, which must be used inside
encrypted containers as messages between users.

Message resources reserve the following properties:

###### `message` *(string, required)*

The message content, which *must* be formatted as [markdown](http://commonmark.org/).

---

### Message Receipt ([schema: `message_receipt`][schema_message_receipt])

When a user receives a message, they *should* send back a message receipt to the original sender,
notifying them that the message was received. This receipt must be used inside an encrypted container.

Message receipt resources reserve the following properties:

###### `receipt` *(string, required)*

The [resource identifier](#resource-identifier) of the [message](#message) received.

---

### Encrypted ([schema: `encrypted`][schema_encrypted])

Encrypted content in a resource is stored inside the root property `encrypted`, and must
be a JSON object conforming to the
[JSON Web Encryption (JWE)](http://self-issued.info/docs/draft-ietf-jose-json-web-encryption.htm),
using the
[JWE JSON Serialization](http://self-issued.info/docs/draft-ietf-jose-json-web-encryption.html#rfc.section.7.2).

All encrypted content *must* also be a fully valid [SDMP container](../container/).

Encrypted resources reserve the following properties:

*(Please refer to the JWE specifications for the most up to date information. Any difference between
the JWE and the following summary should be considered an error, with the JWE taking precedent.)*

###### `encrypted` *(object)*

Holds the keys and encrypted content as a valid JWE object.

###### `encrypted.protected` *(string, required)*

Integrity protected shared header contents. This is equivalent to a `JSON.stringify` of the
header contents, whose octets are
[unpadded base64url](https://tools.ietf.org/html/rfc4648#section-5) encoded.

###### `encrypted.unprotected` *(object, required)*

Shared header contents indicating the type of encryption. See the JWE specifications for details.

###### `encrypted.aad` *(string)*

Additional authenticated data contents.

###### `encrypted.iv` *(string)*

Initialization vector whose octets are
[unpadded base64url](https://tools.ietf.org/html/rfc4648#section-5) encoded.

###### `encrypted.tag` *(string)*

Authentication tag contents.

###### `encrypted.ciphertext` *(string)*

Ciphertext contents whose octets are
[unpadded base64url](https://tools.ietf.org/html/rfc4648#section-5) encoded.

###### `encrypted.recipients` *(array of objects, required)*

Contains per-recipient information.

###### `encrypted.recipients[].header` *(object)*

Contains additional per-recipient unprotected header information.

###### `encrypted.recipients[].encrypted_key` *(string, required)*

Contains the recipient encrypted key whose octets are
[unpadded base64url](https://tools.ietf.org/html/rfc4648#section-5) encoded.


[w_iso8601]: https://en.wikipedia.org/wiki/ISO_8601
[base64]: https://tools.ietf.org/html/rfc4648#section-5
[schema_resource]: https://github.com/sdmp/sdmp-schema/blob/master/schemas/resource.json
[schema_identity]: https://github.com/sdmp/sdmp-schema/blob/master/schemas/identity.json
[schema_node_information]: https://github.com/sdmp/sdmp-schema/blob/master/schemas/node_information.json
[schema_user_information]: https://github.com/sdmp/sdmp-schema/blob/master/schemas/user_information.json
[schema_relationship]: https://github.com/sdmp/sdmp-schema/blob/master/schemas/relationship.json
[schema_post]: https://github.com/sdmp/sdmp-schema/blob/master/schemas/post.json
[schema_message]: https://github.com/sdmp/sdmp-schema/blob/master/schemas/message.json
[schema_message_receipt]: https://github.com/sdmp/sdmp-schema/blob/master/schemas/message_receipt.json
[schema_encrypted]: https://github.com/sdmp/sdmp-schema/blob/master/schemas/encrypted.json
