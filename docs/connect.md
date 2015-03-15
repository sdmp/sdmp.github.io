---
title: connect
subtitle: Initial connection to an SDMP node.
layout: page
---


(In this document, the *local node* is the node initiating the
connection, and the *remote node* is the node to which the
local node is connecting.)

Except for the 

## Session key

A symmetric connection-unique [AES key](./cryptography) is created
for the session.

By using the Diffie-Helman key exchange protocol, this session key
is has as much uniqueness as the *local node* can guarantee. (That is,
the remote node could re-use values for the key exchange, but if the
local node uses unique values the final key will be session-unique.)

### Typical Diffie-Helman

1. Alice and Bob agree to use `p` and `g`
2. Alice chooses secret `a` and sends Bob `A`, where `A=(g^a)%p`
3. Bob chooses secret `b` and sends Alice `B`, where `B=(g^b)%p`
4. Alice computes `s`, where `s=(B^a)%p`
5. Bob computes `s`, where `s=(A^b)%p`
6. Alice and Bob share the secret `s`, which is the session key

### "Wrapped" Diffie-Helman

In order to avoid a MITM attack, the values used in the Diffie-Helman
exchange are signed by the creator, and encrypted to the receiver.

1. Alice has previously acquired and verified Bob's public key `bp`
2. Bob has previously acquired and verified Alice's public key `ap`
3. Alice and Bob agree to use `p` and `g`
4. Alice creates an encrypted message for Bob
	1. Alice chooses secret `a` and calculates `A`, where `A=(g^a)%p`
	2. Alice signs `A` as `S_A` and encrypts `A,S_A` to Bob's public key as `{A,S_A}_bp`
	3. Alice sends Bob the encrypted message `{A,S_A}_bp`
5. Bob creates an encrypted message for Alice
	1. Bob chooses secret `b` and calculates `B`, where `B=(g^b)%p`
	2. Bob signs `B` as `S_B` and encrypts `B,S_A` to Alices's public key as `{B,S_B}_ap`
	3. Bob sends Alice the encrypted message `{B,S_B}_ap`
6. Alice decrypts `{B,S_B}_ap`, verifies `S_B`, and then computes `s`, where `s=(B^a)%p`
7. Bob decrypts `{A,S_A}_bp`, verifies `S_A`, and then computes `s`, where `s=(A^b)%p`
8. Alice and Bob share the secret `s`, which is the session key

## Connection package

The node chooses a secret `n` and calculates `N` where `N=(g^n)%p`.

The values of `g`, `p`, and `N`, along with the [key fingerprint](./cryptography) of
the node public key, are placed inside a YAML stream constructed according to the
following rules:

* The YAML stream contains a single document.
* At the root of the YAML stream is a single object named `connection`.
* The `connection` object properties are: `g`, `p`, `N`, and `node` (the key fingerprint value).
* The values of `g` and `p` are integer-encoded.
* The value of `N` is `binary` encoded, according to [YAML specs](http://yaml.org/type/binary.html).
* The value of `node` is the string-encoded hex value of the key fingerprint.

This YAML stream is transformed into a [SYML](./signed-yaml) file, being signed using
the key identified by the key fingerprint.

Before being sent over the network, the SYML file is encrypted to the public key of the
recipient node, using the following rules:

* The SYML stream is zero-byte padded to the nearest `4096` byte block.
* This stream is encrypted using the public key of the intended recipient node.

This encrypted stream is the connection package, `{g,p,N,S_N,node}_pk`.

## Connection package validation

When a node receives a connection package, it drops the connection under the
following circumstances:

* If the connection package cannot be decrypted by the node.
* If the node cannot verify the signature in the SYML for **any** reason, including
	- malformed data,
	- an improperly generated signature, or
	- the node does not have the public key of the node identified by the fingerprint.
* If the node does not accept the values of `g`, `p`, or `N` for any reason.

## Connection dropping

When dropping a connection during connection package validation, the following process
is followed in **all** cases:

1. The plaintext message `nope` is transmitted.
2. The connection is then dropped.

It is **not** permitted that a node transmit **any** other information concerning
the cause of the drop.

## Connection procedure

The process for establishing a secure connection is as follows:

1. The local node creates a TCP connection to the remote node.
2. The local node sends a connection package to the remote node.
3. The remote node verifies the connection package, dropping the connection if there is an error.
4. The remote node sends a connection package to the local node.
5. The local node verifies the connection package, dropping the connection if there is an error.
6. The local and remote node calculate the shared session key.

All messages past this point are encrypted to this session key.
