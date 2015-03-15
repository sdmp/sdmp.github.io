---
layout: index
---


SDMP is a protocol for distributing cryptographically secure messages. Connections are entirely
peer-to-peer, and all transmissions are strongly encrypted.

Read more:

* [How to construct messages.](/docs/message) (It's basically a signed [YAML](yaml) document.)
* [How to connect to a peer.](/docs/connect) (A combination of [PGP](pgp) and [Diffie-Helman key exchange](diffiehelman).)
* [How to send messages.](/docs/sync) (Think of it as a distributed [WORM](worm)-meets-[CRUD](crud)-meets-[BitTorrent](bittorrent).)
* If that's not enough, read [the entire documentation](/docs)!

# The purpose

At it's heart the SDMP is just a way for machines to communicate to each other in
a very secure way that that humans can understand.

If humans can understand it, they can debug it easier, and make sure the secure
part is actually secure.

The SDMP is the foundation for the [relayreader](relayreader) project.

# Use it

You can use it from the [command line](sdmpcli), or there's a JavaScript [module](sdmpnpm)
you can use.

# Get involved

* You can read through the [docs](/docs) to make sure they are readable and sane.
* [File issues](sdmpissues) if you find anything wrong.
* Create [pull requests](sdmppullrequest) if you have changes you'd like to make.

# License?

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
