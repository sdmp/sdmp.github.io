---
layout: schema
title: session_key
subtitle: Used to establish a symmetric key for encrypting network traffic.
---

The SDMP uses the [Diffie-Helman][w_diffiehelman] key exchange as the way to
establish session keys for [forward secrecy][w_forward] of network traffic.





TODO

this needs to be changed so the diffie-helman key properties are inside
a JWS or whatever that gets hashed independent of the public/private keys

that way on the network connection, it can just send a signature of the
hash of the properties, instead of sending the signed object all over again

when you update this, be sure to update `/network` as well







---

## Diffie-Helman Key Exchange

The generalized [Diffie-Helman][w_diffiehelman] key exchange looks like:

1. Alice and Bob agree in advance to use the values `p` and `g`
2. Alice chooses secret `a` and sends Bob `A`, where `A=(g^a)%p`
3. Bob chooses secret `b` and sends Alice `B`, where `B=(g^b)%p`
4. Alice computes `s`, where `s=(B^a)%p`
5. Bob computes `s`, where `s=(A^b)%p`
6. Alice and Bob share the secret `s`, which is the session key

---

## Schema: `connection` ([JSON Schema][schema])

Contains the values necessary to establish a [Diffie-Helman][w_diffiehelman]
key exchange.

The [container](/schema/container) object contains the following properties:

###### `sdmp.schemas` *(array of strings, required)*

Must be a single array entry with the value: `connection`

This means that placing any additional information or metadata in the
[container](/schema/container) object beyond what is specified for the `container`
and `connection` schema is considered an error.

###### `connection` *(object, required)*

Holds the parameters used in the Diffie-Helman key exchange.

###### `connection.g` *(integer, required)*

The value `g` from the Diffie-Helman key exchange.

###### `connection.p` *(string, required)*

The value `p` from the Diffie-Helman key exchange, whose octets are
[unpadded base64url][base64] encoded.

###### `connection.N` *(string, required)*

The value `N` from the Diffie-Helman key exchange (where `N=(g^n)%p`) whose
octets are [unpadded base64url][base64] encoded.


[w_diffiehelman]: https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange
[w_forward]: https://en.wikipedia.org/wiki/Forward_secrecy
[schema]: https://github.com/sdmp/sdmp-schema/blob/master/schemas/connection.json
[base64]: https://tools.ietf.org/html/rfc4648#section-5
