---
title: Monthly Read Posts in Aug 2019
categories: PROGRAMMING
date: 2019-09-01 20:13:28
tags: [lock-free, memory fence, atomic, mysql, systemd]
---
## Concurrency

[A Lock-Free... Linear Search?](https://preshing.com/20130529/a-lock-free-linear-search/)

实现一个 lock-free array list。

可以作为更复杂的 lockess hashtable 的一个基底实现

---

[The World's Simplest Lock-Free Hash Table](https://preshing.com/20130605/the-worlds-simplest-lock-free-hash-table/)

上一篇的应用。

因为核心是 lockless linear search，所以这里的 hashtable 自然是用 open-addressing 来解决冲突了。

另外两个核心点分别是：
1. 利用 murmurhash3 finalizer 做 hash function
2. 利用 `idx &= len - 1` 其中 `len` 为二次幂来做 wrap around。

---

[Atomic vs. Non-Atomic Operations](https://preshing.com/20130618/atomic-vs-non-atomic-operations/)

这次考虑的是 non-atomic load/store operations

---

[The Happens-Before Relation](https://preshing.com/20130702/the-happens-before-relation/)

happens-before: If operation A happens-before operation B, then the memory effects of A effectively become visible to the thread performing B before B performs.

1. Happens-before doesn't imply happening before: no violation if memory effects visibility preserved
2. Happening before doesn't imply happens-before: the happens-before relationship only exists where the language standard says it exists.

---

[The Synchronizes-With Relation](https://preshing.com/20130823/the-synchronizes-with-relation/)

guard-variable & payload metaphore

payload: set of data being propagated between threads
guard variable: protects access to the payload.

a write-release can synchronizes-with a read-acquire.

---

[Acquire and Release Fences](https://preshing.com/20130922/acquire-and-release-fences/)

An acquire fence prevents the memory reordering of any read which precedes it in program order with any read or write which follows it in program order.

A release fence prevents the memory reordering of any read or write which precedes it in program order with any write which follows it in program order.

acquire and release fences is that they can establish a synchronizes-with relationship

---

[Acquire and Release Fences Don't Work the Way You'd Expect](https://preshing.com/20131125/acquire-and-release-fences-dont-work-the-way-youd-expect/)

How acquire/release fences differ from acquire/release operations.

## Database

[index merge 引起的死锁分析](http://seanlook.com/2017/03/11/mysql-index_merge-deadlock/)

学到了原来还有 index merge 这种操作。

所以说索引不加好，破事一堆。

## Misc

[Creating a Linux service with systemd](https://medium.com/@benmorel/creating-a-linux-service-with-systemd-611b5c8b91d6)

看起来比使用 supervisor 靠谱
