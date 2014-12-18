---
layout: index
---


The `sdmp` is a protocol for passing [strongly secure](/docs/cryptography) messages
between computers, without the requirement for central or third-party computers
such as IMAP servers. This is done using a [network protocol](/docs/network) which
uses pre-distributed public/private keys.

User generate messages or other [resources](/docs/resources) and publish them directly
to other users with simple [CRUD-like actions](/docs/actions).

All resources passed through the `sdmp` are [signed tar packages](/docs/signed-tar),
which give a cryptographically strong verification of the publishing user.

In place of the PGP "key signing", the `sdmp` uses a social network metaphor to establish
key relationships. These relationships are created using [sdmp-specific controls](/docs/controls),
which are documents published as resources.

View the [complete docs](/docs/).
