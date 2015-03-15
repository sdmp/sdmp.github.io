---
layout: index
---


SDMP is a protocol for distributing cryptographically secure messages. Connections are entirely
peer-to-peer, and all transmissions are strongly encrypted.

There are two identities: the *user* and the *node*.

* The *user* is the actual person or organization, and has one master public/private key.
* The *node* is the application used to create and transmit messages, and has a separate public/private key.

A *user* authorizes a *node* to publish messages. This means a *node* operating on a laptop does
not need to have the public/private key of the *user* accessible on that laptop.

Read more:

* [How to construct messages.](./docs/message) (It's basically a signed [YAML](yaml) document.)
* [How one node connects to another node.](./docs/connect) (A spin on the [Diffie-Helman key exchange](diffiehelman).)
* [How to send messages.](./docs/sync) (Think of it as a distributed [WORM](worm)-meets-[CRUD](crud)-meets-[BitTorrent](bittorrent).)
* If that's not enough, read [the entire documentation](./docs)!

# The purpose

At it's heart the SDMP is just a way for machines to communicate to each other in
a very secure way that humans can understand.

If humans can understand it, they can debug it easier, and make sure the secure
part is actually secure.

# Use it

* You can use it from the [command line](sdmpcli)
* There's a JavaScript [module](sdmpnpm) you can use
* Check out the [relayreader](relayreader) project

# Get involved

* You can read through the [docs](./docs) to make sure they are readable, consistent, and sane.
* [File issues](sdmpissues) if you find anything wrong.
* Create [pull requests](sdmppullrequest) if you spot anything wrong or missing and want to propose a fix.

## Release Cycles

The SDMP is currently in **alpha** stage. Once development has moved to a **beta** stage, releases
will be done using [Semantic Versioning 2.0.0](http://semver.org/).

# License

All code, documentation, and assets available under the entire [SDMP repo](sdmprepo)
are published and released under the [Very Open License](vol).

<3

[sdmpcli]: https://github.com/sdmp/sdmp-cli
[sdmpnpm]: https://github.com/sdmp/sdmp-npm

[relayreader]: http://relayreader.com
[sdmpissues]: https://github.com/sdmp/sdmp.github.io/issues
[sdmppullrequest]: https://github.com/sdmp/sdmp.github.io/pulls
[sdmprepo]: https://github.com/sdmp
[vol]: http://veryopenlicense.com/
[yaml]: https://en.wikipedia.org/wiki/YAML
[pgp]: https://en.wikipedia.org/wiki/Pretty_Good_Privacy
[diffiehelman]: https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange
[worm]: https://en.wikipedia.org/wiki/Write_once_read_many
[crud]: https://en.wikipedia.org/wiki/Create,_read,_update_and_delete
[bittorrent]: https://en.wikipedia.org/wiki/BitTorrent
