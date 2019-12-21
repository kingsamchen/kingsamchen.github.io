---
title: SYN Queue and Accept Queue
categories: PROGRAMMING
date: 2019-12-21 22:23:19
tags: [tcp, listen, backlog]
---
## 0x00 A Single Queue or Two Queues

TCP/IP stack has two options to implement the backlog queue for a socket in the `LISTEN` state.

**Door #1: A Single Queue**

A single queue can contain connections in two different states:

- SYN RECEIVED
- ESTABLISHED

And only connections in `ESTABLISHED` state can be returned to the application by calling the `accept()` syscall.

Therefore, the length of this queue is determined by the `backlog` argument of the `listen()` syscall.

**Door #2: A SYN Queue & An Accept Queue**

In this case, connections in state `SYN RECEIVED` are added to the SYN queue, and later moved to the accept queue when their state changes to `ESTABLISHED`.
<!-- more -->
So, the accept queue stores only connections ready for the `accept()` syscall.

Unlike the previous case, the backlog argument of the `listen()` syscall determines the size of the accept queue.

### BSD's Choice

BSD chooses the option 1 as its behavior implementation (though it internally indeed uses two queues).

When the queue is full, instead of sending back a SYN/ACK packet in response to received SYN packet, it simply drops the SYN packet, and let the client to retry.

### Modern Linux's Choice

Since Linux 2.2, Linux uses the 2nd option and make

- `backlog` argument for the maximum length of the accept queue
- `/proc/sys/net/ipv4/tcp_max_syn_backlog` for the maximum length of SYN queue; and newer kernel uses `/proc/sys/net/core/somaxconn` (identified via `net.core.somaxconn`)

![](/img/syn-and-accept-queues.png)

By the way, from the point of the view of the client, a connection will be in state `ESTABLISHED` after reception of the SYN/ACK packet.

## 0x01 When SYN Queue is Full

When received a SYN packet but SYN queue is full, then:

- If `net.ipv4.tcp_syncookies=0`, then the SYN packet is simply discarded
- If `net.ipv4.tcp_syncookies=1`, then
    a. if the accept queue is full AND req_young_len > 1, then the SYN packet is discarded
    `req_young_len` is the number of connections that haven't been retransmitted SYN/ACK packet in SYN queue
    b. otherwise, generate syncookies for this SYN packet; This packet is presumed a malicous packet.

## 0x02 When Accept Queue is Full

When received an ACK packet, but the accept queue is full:

- If `tcp_abort_on_overflow=1`, then the TCP/IP stack replies back a RST packet, then the connection is removed from the SYN queue.
- If `tcp_abort_on_overflow=0`, then the stack marks the connection as ACKED (i.e. ignores this ACK packet) and leaves the connection in SYN queue; soon, the timer would then go off and resend a SYN/ACK packet back, the client can resend an ACK packet again.
    Or if the maximum retry limit is reached, a RST packet is still replied and the connection is removed from the SYN queue

The maximum retry number is configured by `net.ipv4.tcp_synack_retries`.

However, if the accept queue is full, the kernel will also impose a limit on the rate at which SYN packets are accepted: If too many SYN packets are received, some of them will be dropped.

In this case, it is up to the client to retry sending the SYN packet.

## 0x03 Disadvantages of Using a Single Queue

There are two major causes of the single queue that might be full.

(1) Application calling `accept()` is not fast enough, making completed (`ESTABLISHED`) connections fill the queue
(2) RTT between the server and the client is so big, that incomplete connections (in state `SYN RECEIVED`) fill the queue.

By using one queue, it assumes that an application is expected to tune the backlog

- not only taking into account how it intents to process newly established incoming connections,
- but also in function of traffic characteristics such as the round-trip time.

If we use two dedicated queues, the SYN queue then effectively implies ACK packets in flight; and the accept queue implies how application level handles the completed connections.
