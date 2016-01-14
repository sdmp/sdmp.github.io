---
layout: article
title: Roadmap
subtitle: Where this is going and how it will get there.
---


## Short Term

* `0.12`: Clear and readable core specs. *Completed!*
* `0.13`: All crypto specs clear and tested.
* `0.14`: TCP implementation has been finished.

During beta development, backwards compatability
between versions is not guaranteed.

---

## Overview

* `0.x`: Development stage. *In progress!*
* `1.0`: First non-beta release.
* `2.0`: Changes after real-world users play with it.

---

## Details

After the initial beta work is done, the roadmap milestones
will look like this:

### `1.0`

This version will have been verified as usable by a command
line implementation, and will have had several people read
through it to make sure it is reasonable. However, it will
not have been through much "real world" user testing.

* Containers and all schemas are clearly specified.
* Public/private key pairs are well defined.
* How to establish session keys is well defined.
* Basic TCP specs are *functional*, but may not be
  optimized for efficiency.
* Example modules and libraries will have been written
  in at least a few common programming languages.

### `1.1`

After the `1.0` release, work is planned for a GUI
which will make the protocol easier to use for normal
people: things like finding users and verifying keys.

The `1.1` release will include changes that are proposed
during the development of various applications.

Changes between `1.0` and `1.1` will be *non-breaking*.

## `1.2+`

After the `1.1` release, additional releases may be
made if there is enough request for them, but these
releases will all be *non-breaking*.

### `2.0`

After many applications have been developed, a request for
comments will be made, asking for more detailed discussion
on limitations and problems encountered with the protocol.

Changes to the protocol will be proposed and reviewed, and
a new version of the protocol will be released.

Although attempts will be made for backwards compatability,
changes between `1.x` and `2.0` *may* be breaking.
