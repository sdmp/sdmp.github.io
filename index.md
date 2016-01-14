---
layout: index
---


A protocol for distributing cryptographically secure messages across an
encrypted peer-to-peer network.

---

## Purpose

At its heart the SDMP is just a way for machines to communicate messages to each
other in a secure way that humans can understand.

If humans can understand it, it's easier to make sure the secure part is actually secure.

---

## Specification

**Current release is: [0.12](/spec/0.12)**

Details from older releases are available [on Github][repo].

---

## Projects

* [sdmp-cli](https://github.com/sdmp/sdmp-cli): The authoritative implementation of the specs.

---

## Versioning

Updates to the specifications will follow [Semantic Versioning 2.0.0][semver].

> **Note:**
>
> Major version zero (`0.y.z`) is for initial development. Anything may
> change at any time. The public API should not be considered stable.

---

## Roadmap

Read more [here](/roadmap), but the summary is:

* `0.12`: Clear and readable core specs. *Completed!*
* `0.13`: All crypto specs clear and tested.
* `0.14`: TCP implementation has been finished.
* `1.0`: First non-beta release.
* `2.0`: Changes after real-world users play with it.

---

## Licensing

The entirity of the SDMP project is released under the [Very Open License][vol].

---

## Get Involved

What is needed most of all are people to read through the
[latest revision](/spec) and make sure it's human-readable,
consistent, secure, and sane.

* Found something wrong or questionable? [File an issue!][issues]
* Have questions? Go ahead and [file an issue][issues] for that, too.
* Want to propose a change? You can make a [pull request][pulls]!

Want to get even more involved? Read more about
[how to contribute](https://github.com/sdmp/sdmp.github.io/blob/master/CONTRIBUTING.md).


[repo]: https://github.com/sdmp
[vol]: http://veryopenlicense.com/
[semver]: http://semver.org/
[issues]: https://github.com/sdmp/sdmp.github.io/issues
[pulls]: https://github.com/sdmp/sdmp.github.io/pulls
[multimark]: https://en.wikipedia.org/wiki/MultiMarkdown
