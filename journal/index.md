---
layout: docs
title: Journal
subtitle: Each node maintains a journal of resources it knows about.
---


Each node maintains an ordered list of the [resources](/resource) it publishes,
as well as all resources published by any [trusted node](/schema/relationship).
This ordered list is called the *node journal*, is unique per node, and is the
thing that is kept [synchronized](/synchronization) between nodes.

An example journal [is available here](#example-journal).

---

## Journal Entries

The first line of the node journal is always the
[key fingerprint](/cryptography#key-fingerprint)
of the node, and each following line of the node
journal is constructed by concatenating:

* the *journal line identifier*, then
* a single `@` character, then
* the [key fingerprint](/cryptography#key-fingerprint) of the node
	publishing the resource, then
* a single forward slash character (`/`), then
* the [resource identifier](/resource#resource-identifier) of the
	resource being published.

View example journal entries [here](#example-journal).

---

## Journal Line Identifier

Except for the very first entry of the journal, the *journal line identifier*
is the [hash](/cryptography#hashing) of the previous line's characters,
excluding any newline characters, whose octets are
[unpadded base64url](https://tools.ietf.org/html/rfc4648#section-5)
encoded.

---

## When to Write

The node writes to its own journal under the following circumstances:

* When the node creates a new resource, it appends a new entry to
	its journal for that resource.
* Whenever the node receives a resource published by a
	[connection](/schema/relationship), it appends a new
	entry to its own journal for that resource.

The ordering of the journal is by knowledge of the resource, *not* by
clock time of resource publication. For example, if a node receives
notice of a published resource several days after the publication,
the resource identifier for that resource is still added to the *end*
of the journal.

A node should *not* write to the journal until it has received and
verified the resource.

---

## Example Journal

For a user with a key fingerprint of:

	GlvAreTo0lCSyum7Wzh8pzhxYOOu-gMIgO2N95AAwAGP6-nR8xCvWvIW0t9rF_ZZfpCY_fDV38JDFKaOU91A8Q

And a node with a key fingerprint of:

	0lCSyum7GlvAreToWzh8pzhxgO2N95AAYOOu-gMIwAGP6-nR0t9rF_ZZ8xCvWvIWfpCY_fDVU91A8Q38JDFKaO

If the node starts by publishing two resources from that user, and those resource identifiers were:

	reTo0GlvAlCSyzh8pum7WzhxYgMIgOOu-O2N9AGP65AAw-nR8vIW0xCvWt9rFpCY__ZZffDVJOU91DFKaA8Q38
	vAlCSyzreTo0Glh8pum7WgOOu-O2zhxYgMIN9AGP65vIW0xCvAAw-nR8Wt9rFpCDVJOU91Y__ZZffDFK38aA8Q

The complete node journal would look like this (view the raw text
file [here](/journal/example-journal.txt)):

	0lCSyum7GlvAreToWzh8pzhxgO2N95AAYOOu-gMIwAGP6-nR0t9rF_ZZ8xCvWvIWfpCY_fDVU91A8Q38JDFKaO
	22tfYa3Xy3-G2vbAh4vxiJPqJk4iLU6FBmqx5Tsw7rGgEWxEHmocPhRZ2s-6Ww1na3DV6JdwU22OJukbtfDXDw@GlvAreTo0lCSyum7Wzh8pzhxYOOu-gMIgO2N95AAwAGP6-nR8xCvWvIW0t9rF_ZZfpCY_fDV38JDFKaOU91A8Q/reTo0GlvAlCSyzh8pum7WzhxYgMIgOOu-O2N9AGP65AAw-nR8vIW0xCvWt9rFpCY__ZZffDVJOU91DFKaA8Q38
	MQhBjDUYMP5GsykDDlUQbSabPZ7U2dY-VqxKTghDSfFlLQAW0Y-tuaHD-GgeyxGOnYFkNXW_5bP0T6TG4vSl5w@GlvAreTo0lCSyum7Wzh8pzhxYOOu-gMIgO2N95AAwAGP6-nR8xCvWvIW0t9rF_ZZfpCY_fDV38JDFKaOU91A8Q/vAlCSyzreTo0Glh8pum7WgOOu-O2zhxYgMIN9AGP65vIW0xCvAAw-nR8Wt9rFpCDVJOU91Y__ZZffDFK38aA8Q
