---
layout: index
---


The SDMP is a protocol for passing [strongly secure](/docs/cryptography) messages
between computers, without the requirement for central or third-party computers
such as IMAP servers.

[Resources](/docs/resources) passed between computer nodes are [signed tar packages](/docs/signed-tar),
which give a cryptographically strong verification of the publishing user.

Resources are published to other computers with simple [CRUD-like actions](/docs/actions), and can
be created as "public" (the default) or [private and encrpyted](/docs/encrypted-resources).

User relationships are created using [sdmp-specific controls](/docs/controls),
which are descriptive YAML documents published as resources.

A strongly secure [network protocol](/docs/network) is specified, which has
some particular differences that guarantee end-to-end network traffic
encryption.

View the [complete docs](/docs/).
