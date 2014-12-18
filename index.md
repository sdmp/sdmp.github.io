---
title: secure distributed messaging protocol
description: Specifications and implementation for the secure distributed messaging protocol (sdmp).
layout: default
---


### Current status: `alpha`

The SDMP is a protocol for passing [strongly secure](cryptography) messages
between computers, without the requirement for central or third-party computers
such as IMAP servers.

[Resources](resources) passed between computer nodes are [signed tar packages](signed-tar),
which give a cryptographically strong verification of the publishing user.

Resources are published to other computers with simple [CRUD-like actions](actions), and can
be created as "public" (the default) or [private and encrpyted](encrypted-resources).

User relationships are created using [sdmp-specific controls](controls),
which are descriptive YAML documents published as resources.

A strongly secure [network protocol](network) is specified, which has
some particular differences that guarantee end-to-end network traffic
encryption.

## Table of Contents

* [Cryptographic standards](cryptography)
* [Signed tar files](signed-tar)
* [Resources](resources)
* [Encrypted resources](encrypted-resources)
* [URI scheme](uri-scheme)
* [Actions: create, update, delete](actions)
* [Controls: user, node, connection](controls)
* [Network](network)
    - [Connection](connect)
    - [Request/response](request)

## Contribute

* Familiar with cryptography? Check for potential security issues!
* Familiar with network protocols? Review the [network specs](network)!

If you have suggestions or critiques, please file them by creating
a new [GitHub issue][issue].

[issue]: https://github.com/sdmp/sdmp.github.io/issues

## Release Cycles

The SDMP is currently in **alpha** stage.

Coding of a basic implementation has begun! This service will
perform the operations written in the specs as precisely as possible.

As issues arise, they will be created as Github issues and the technical
hurdles will be documented as thoroughly as possible. Once a pull request
is prepared which resolves those specific issues, it will be merged immediately
and there will **not** be a waiting period.

After development, releases will use [Semantic Versioning 2.0.0](http://semver.org/).
