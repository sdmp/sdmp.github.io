---
layout: article
title: Journal Heartbeat
subtitle: The node journal, heartbeat queue, and heartbeat broadcast.
---




Each node maintains a journal which represents the complete list of messages it
publishes, as well as all other node messages known to the node. The journal is
simply a list of author+message digests recorded in the order in which they
were known (aka not by date).

The heartbeat broadcast is a UDP broadcast from a node, regulated in such a way that
the bandwidth does not exceed a user configured throttle, and sent to known nodes
in a randomized order.

Sending a heartbeat broadcast to another node is done by creating a SYML message, signed
by the broadcasting node, and encrypted to the recipient node. This data is sent over
UDP as binary data to the recipient node, with no confirmation of message delivery.

The heartbeat queue is a list of messages to be sent to specific nodes. These messages
are not prioritized. When a node prepares to broadcast a heartbeat, it randomly selects
a node to send data to. It then checks the heartbeat queue, and if there are any
messages in the queue intended for the selected node, these are prepared and sent
instead of the normal heartbeat broadcast.

A message on the heartbeat queue can be completely prepared in advance, by creating
the message, signing it, and encrypting it to the recipient node. This should be
done to throttle CPU use, so that messages can be prepared in CPU downtime.

Message delivery confirmation is done by waiting for a node to send you a heartbeat
broadcast with a journal listing containing your message.
 i
When a node prepares a normal heartbeat broadcast, it does so by creating a SYML
message containing the N most recent entries to its journal, where N is some sane
number, such as 20 or 50 or 100 (whatever the packet size should be). This message
is broadcast to nodes so that they know if they missed previous messages during downtime.

## Benefits

By using a heartbeat broadcast, nodes are kept in synchronization, without needing
to make periodic requests, aka RSS. In this sense, this is much closer to the
publisher/subscriber pattern.

By using a randomized selection to decide which node to broadcast to, some semblance
of anonymization is made. If messages are properly padded, there will be no indication
that a message received generated a response to another node. Because of this, the
small-world-network privacy issues are reduced. (Not eliminated, but much closer.)

## Problems

Because sending out a message is not over TCP, there is not a network-level confirmation
of a message delivery.

Because the order of messages is not prioritized, sending a private message may not
be very fast. A message to a single node could sit on the message queue for a long
while before it was broadcast to the recipient.

> **Note:**
> If the bandwidth is throttled to 10Kb/s, and each hartbeat is 60Kb, a complete heartbeat
> broadcast happens once per minute. If a user has knowledge of 1440 nodes, this means
> each node might expect about 10 heartbeats per day, i.e. one every couple hours. In
> general this won't be the case. E.g. Filezilla on a slow network is often limited to
> 100Kb/s, and an average user is likely to only know about 500 nodes. In this example,
> if each heartbeat is 60Kb, each node might expect to see about 280 heartbeats per day,
> or one every few minutes. Additional speedups are possible by pruning known nodes.
