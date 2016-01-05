---
layout: docs
title: journal
subtitle: Nodes create resources and publish them to a journal.
---


Journals are lists of known published data. Data is published
inside resource objects, which are [signature](/core/signature)
objects containing information about the publisher of the data.

## [Structure](./structure)

Each node maintains its own journal, which is a list of known
resources, ordered by the time the node verified the resource.

## [Resources](./resource)

Journal entries reference published resources, which are
[signature](/core/signature) objects containing valid
[containers](/core/container) with some additional information
about the publisher.

## [Broadcast](./broadcast)

Nodes periodically broadcast a list of recently added journal
entries to all [trusted](/trust) nodes.

## [Request Journal](./request_journal)

Nodes may also request the journal entries of another node.

## [Request Resource](./request_resource)

Nodes do not automatically transmit newly verified resources. Instead,
each node must request resources they do not have.

## [Response](./response)

Nodes respond to resource or journal entry requests with
resource objects.
