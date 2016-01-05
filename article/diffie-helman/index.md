---
layout: article
title: Using Diffie-Helman
subtitle: Why the SDMP uses the Diffie-Helman to establish session tokens.
---







TODO: if using elliptic curves, there's a DH method
	https://en.wikipedia.org/wiki/Elliptic_curve_Diffie%E2%80%93Hellman



Another benefit of the DH is that, even if another node is using
poorly generated random numbers, the initiating node can have some
bit of assurance that the session token is still strong-ish if
its own numbers are good.






Using the same encryption key for all messages has a fundamental problem: if the key
is ever compromised, all previous messages may be decrypted. This problem is well
known, and is often referred to as the problem of [forward secrecy][w_forward].

Arguably, one of the most well known solution to this problem is the
[Diffie-Helman][w_diffiehelman] key exchange. Because this method is
well explored, the Diffie-Helman key exchange is used as a method for
establishing a network session key. It is proposed as the primary
mechanism for establishing session keys.

---

## Diffie-Helman key exchange review

The generalized [Diffie-Helman][w_diffiehelman] key exchange looks like:

1. Alice and Bob agree in advance to use the values `p` and `g`
2. Alice chooses secret `a` and sends Bob `A`, where `A=(g^a)%p`
3. Bob chooses secret `b` and sends Alice `B`, where `B=(g^b)%p`
4. Alice computes `s`, where `s=(B^a)%p`
5. Bob computes `s`, where `s=(A^b)%p`
6. Alice and Bob share the secret `s`, which is the session key

---

## Self signed Diffie-Helman

Within the SDMP, the two communicating nodes must have each others public keys before
communicating, therefore the exchanged values are self signed.

If Alice and Bob have previously exchanged and validated each others public keys, they
will be able to validate each others cryptographic signatures. Instead of relying on
a third party to assure key ownership, each party can validate the other party's
signature.

To verify an exchanged key, Alice would sign `A` and transmit the signature and the
value `A`. With this information, Bob is able to verify the authorship of `A`.
Likewise Bob would sign `B` and transmit the signature and the value `B`, and with this
information Alice is able to verify the authorship of `B`.

---

## Establishing a connection

When two nodes connect, they establish a session token using the 








### Connection message

Each node creates a *connection message* which is a [SYML][syml] stream containing
the information necessary for the Diffie-Helman exchange, signed by the node
creating the connection message.

All connection messages have, at the root of the YAML document, a single object
named `sdmp` with the following required properties:

###### `sdmp.version` *(number, required)*

The version of the SDMP specifications used to generate the communication document. Currently
this value is the number `0.9` exactly.

###### `sdmp.created` *(string, required)*

The [ISO 8601][w_iso8601] formatted date that the resource was created, with granularity to the
minute of the hour, e.g. `2015-04-25 13:10`. This value is primarily used for human readability,
since it cannot be cryptographically verified.

###### `sdmp.connection` *(object, required)*

Used to hold connection parameters.

###### `sdmp.connection.g` *(integer, required)*

The value `g` from the Diffie-Helman key exchange.

###### `sdmp.connection.p` *(binary, required)*

The [binary encoded][yaml_binary] value `p` from the Diffie-Helman key exchange.

###### `sdmp.connection.N` *(binary, required)*

The [binary encoded][yaml_binary] value `N` from the Diffie-Helman key exchange,
where `N=(g^n)%p`.

### No additional data

Placing any additional information or metadata in the connection message--in particular
information which could identify the node creating the message--is considered an error.

If this state is detected the connection must not be used.

### Connection message validation

When a node receives a connection message, if the following circumstances are detected the
connection must not be used:

* If the connection message cannot be decrypted by the receiving node.
* If the node cannot verify the signature in the message for any reason, including but not limited to:
	- malformed data,
	- an improperly generated signature,
	- the node does not have the public key of the node which generated the message.
* If the receiving node does not accept the values of `g`, `p`, or `N` for any reason.

### Connection dropping

When dropping or refusing a connection during connection message validation, it is not
permitted that the node transmit any information concerning the cause of the drop.

### Network traffic

After both nodes have exchanged connection messages, they have the necessary information to calculate
the shared session key and to verify the signature of the message.

After this point, all connection network traffic must be encrypted using this established key.

### Transmitting data

The following ordered steps must be performed prior to transmitting any data across the network:

1. The data is compressed using the [DEFLATE][deflate] algorithm specified in [RFC 1951][rfc1951].
2. The compressed data must be [zero-byte padded](../cryptography) to the nearest 256 bytes.
3. The compressed and padded data must be encrypted using the calculated session key.

Received data follows the reverse of those steps.


[w_diffiehelman]: https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange
[w_forward]: https://en.wikipedia.org/wiki/Forward_secrecy
[w_iso8601]: https://en.wikipedia.org/wiki/ISO_8601
[yaml_binary]: http://yaml.org/type/binary.html
[syml]: http://github.com/sdmp/signed-yaml
[deflate]: https://en.wikipedia.org/wiki/DEFLATE
[rfc1951]: https://www.ietf.org/rfc/rfc1951.txt
