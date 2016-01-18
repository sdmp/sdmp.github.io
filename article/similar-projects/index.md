---
layout: article
title: Similar Projects
subtitle: Compare this project to similar projects.
---


This article seeks to answer the question: how does
the SDMP compare to other similar protocols or projects.

---

The SDMP is:

* a language and network agnostic protocol,
* used to distribute encrypted messages between users,
* without requiring always-on servers (e.g. IMAP),
* and simple enough that humans can understand it.

Other projects have started since this one began, sometime back
in 2012, and some are working on a similar problem, so this is
an attempt to catalog the differences between similar projects.

> *Note:*
>
> If you know of another project not mentioned in here that you
> think is working towards similar goals, please [file an issue][issue]
> and it'll probably get added to this list!

---

## Not a Protocol

Most projects in cryptography seem to start as whitepapers, then
software development. Note that the missing point is the development
of a language agnostic protocol.

For example, a popular bit of software in the JS world is
[socket.io](http://socket.io/), which is a really easy way
to communicate using [websockets](https://developer.mozilla.org/en-US/docs/Glossary/WebSockets).
However, `socket.io` started as a JS library, so that when
developers wanted to implement it in Java or C# or Swift,
they essentially had to reverse-engineer the protocol used.

This isn't meant to excoriate socket.io--there are *many*
valid reasons that developers start with the software and
hammer out the protocol specifics later.

However, one of the main goals of the SDMP is to create
a language agnostic protocol, such that developers can
feel safe creating implementations in whatever language
they are using.

Most projects similar in goals to the SDMP were rejected
because they start with software, not with specifications.

---

## [Keybase](https://keybase.io/)

Keybase has similar goals of making it easier to find
and user another users public key. Their stated goal is:

> Get a public key, safely, starting just with someone's
> social media username(s).

For example, given a Twitter or Github username, you can
find their [account on Keybase](https://keybase.io/saibotsivad),
and have a reasonable assurance that the public key obtained
there is secure.

Keybase is really cool, and highly recommended, but it is
not a secure messaging protocol.
