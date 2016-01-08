---
layout: docs
title: traffic
subtitle: Network communication uses shared keys.
---


Communication over a network is done by sending
[encrypted containers](/core/encrypted), which are
encrypted using the [shared key](/network/shared_key)
established during the [connection handshake](/network/handshake).

---

## Network Communication

An *encrypted network object* is a [container](/core/container) object where
the **only** specified schema is the [encrypted](/core/encrypted) schema,
and **no** other metadata is allowed.

Specifically, the [key fingerprint](/core/cryptography#key-fingerprint)
or any other information about the node initiating the connection
must **not** be exposed in this object.

The [UTF-8][w_utf8] encoded string representation of the JSON object
is sent as the data stream for all network traffic after the session
key is established.

E.g., the data stream sent is the output of the function:

	UTF8_ENCODE(JSON.stringify(Encrypted Container))

Specific network implementations may describe additional encoding
or transforms, such as requiring gzip on the data.

---

## No Additional Data

Placing *any* additional unencrypted information or metadata 
in the containers beyond what is specified by the specific schemas
is considered an error.

If this state is detected at any point, the network connection *must*
be rejected.

---

## Network traffic

After both nodes have completed the [handshake](/network/handshake), they
have established a secure shared session key. At this point, all future
connection network traffic *must* be encrypted using this established key.


[w_utf8]: https://en.wikipedia.org/wiki/UTF-8
