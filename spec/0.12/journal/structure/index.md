---
version: 0.12
parent: journal
layout: spec
title: structure
subtitle: Structure of the journal.
---


Each node maintains an ordered list of the [resources](../../journal/resource)
it publishes, as well as all resources published by any
[trusted node](../../schema/trust).
This ordered list is called the *journal*. It is unique per node, and is
the thing that is kept synchronized between nodes.

An example journal [is available here](#example-journal).

---

## Journal Entries

The first line of the node journal is always the
[key fingerprint](../../core/cryptography#key-fingerprint)
of the node, and each following line of the node
journal is constructed by concatenating:

* the *journal line identifier*, then
* a single `@` character, then
* the [key fingerprint](../../core/cryptography#key-fingerprint) of
  the node publishing the resource, then
* a single forward slash character (`/`), then
* the [resource identifier](../../journal/resource#resource-identifier)
  of the resource being published.

View example journal entries [here](#example-journal).

---

## Journal Line Identifier

Except for the very first entry of the journal, the *journal line identifier*
is the [hash](../../core/cryptography#hashing) of the previous line's
characters, excluding any newline characters, whose octets are
[unpadded base64url](https://tools.ietf.org/html/rfc4648#section-5)
encoded.

---

## When to Write

The node writes to its own journal under the following circumstances:

* When the node creates a new resource, it appends a new entry to
  its journal for that resource.
* Whenever the node receives a resource published by a
  [connection](../../schema/trust), it appends a new entry
  to its own journal for that resource.

The ordering of the journal is by knowledge of the resource, *not* by
clock time of resource publication.

For example, if a node receives notice of a published resource several
days after the publication, the resource identifier for that resource
is still added to the *end* of the journal.

A node should *not* write to the journal until it has received the
resource, and verified its signature.

---

## First Three Entries

The first line of the journal must be the
[key fingerprint](../../core/cryptography#key-fingerprint)
of the node.

The second line of the journal must be calculated using the
[resource identifier](../../journal/resource#resource-identifier)
of the[identity resource](../../core/identity) of the user
authorizing the node.

The third line of the journal must be calculated the
[resource identifier](../../journal/resource#resource-identifier)
of the [trust resource](../../schema/trust) authorizing the node
on behalf of some user.

---

## Example Journal

For a user with a key fingerprint of:

	GlvAreTo0lCSyum7Wzh8pzhxYOOu-gMIgO2N95AAwAGP6-nR8xCvWvIW0t9rF_ZZfpCY_fDV38JDFKaOU91A8Q

And a node with a key fingerprint of:

	0lCSyum7GlvAreToWzh8pzhxgO2N95AAYOOu-gMIwAGP6-nR0t9rF_ZZ8xCvWvIWfpCY_fDVU91A8Q38JDFKaO

And the resource identifier of the [trust resource](../../schema/trust)
authorizing the node is:

	QqsKcr29oKYsY8a4-asK5NT7dQXQ1_BG1b4MbvdXDPLM786YMlrFJdxQy8M2kjzCqzm3tvzQeQptEr_u-fkX_g

If the node then publishes two resources from that user, and those resource identifiers were:

	reTo0GlvAlCSyzh8pum7WzhxYgMIgOOu-O2N9AGP65AAw-nR8vIW0xCvWt9rFpCY__ZZffDVJOU91DFKaA8Q38
	vAlCSyzreTo0Glh8pum7WgOOu-O2zhxYgMIN9AGP65vIW0xCvAAw-nR8Wt9rFpCDVJOU91Y__ZZffDFK38aA8Q

The complete node journal would look like this (view the raw text
file [here](./example-journal.txt)):

	0lCSyum7GlvAreToWzh8pzhxgO2N95AAYOOu-gMIwAGP6-nR0t9rF_ZZ8xCvWvIWfpCY_fDVU91A8Q38JDFKaO
	22tfYa3Xy3-G2vbAh4vxiJPqJk4iLU6FBmqx5Tsw7rGgEWxEHmocPhRZ2s-6Ww1na3DV6JdwU22OJukbtfDXDw@GlvAreTo0lCSyum7Wzh8pzhxYOOu-gMIgO2N95AAwAGP6-nR8xCvWvIW0t9rF_ZZfpCY_fDV38JDFKaOU91A8Q/QqsKcr29oKYsY8a4-asK5NT7dQXQ1_BG1b4MbvdXDPLM786YMlrFJdxQy8M2kjzCqzm3tvzQeQptEr_u-fkX_g
	HXv3ogo2IMA9XETtzZUdOfdTzfrYJNOlYf9NncMHORqWwLiCbMnYy6CVGepdEVlUI5O3AmL57CwZGeXuBSaSk@GlvAreTo0lCSyum7Wzh8pzhxYOOu-gMIgO2N95AAwAGP6-nR8xCvWvIW0t9rF_ZZfpCY_fDV38JDFKaOU91A8Q/reTo0GlvAlCSyzh8pum7WzhxYgMIgOOu-O2N9AGP65AAw-nR8vIW0xCvWt9rFpCY__ZZffDVJOU91DFKaA8Q38
	d5Rk2hM6KGcO9x-bgDPpEnp07euiIK7cDiDlgDN2nH_AuWJQHQqF7KVqRrRomZWhmm4t75s7_Eqb6HIGQUxCCw@GlvAreTo0lCSyum7Wzh8pzhxYOOu-gMIgO2N95AAwAGP6-nR8xCvWvIW0t9rF_ZZfpCY_fDV38JDFKaOU91A8Q/vAlCSyzreTo0Glh8pum7WgOOu-O2zhxYgMIN9AGP65vIW0xCvAAw-nR8Wt9rFpCDVJOU91Y__ZZffDFK38aA8Q
