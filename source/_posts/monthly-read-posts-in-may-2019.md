---
title: Monthly Read Posts in May 2019
categories: PROGRAMMING
date: 2019-06-02 21:40:29
tags: [mutex, lockless, memory reordering, circuit breaker]
---
## Concurrency

[Roll Your Own Lightweight Mutex](https://preshing.com/20120226/roll-your-own-lightweight-mutex/)

如何利用系统提供的 semaphore synchronization primitive 实现一个轻量级的 mutex

---

[Review of many Mutex implementations](http://cbloomrants.blogspot.com/2011/07/07-15-11-review-of-many-mutex.html)

和上面类似，但是使用了 event pritimive 作为基本设施。

因为 event 不像 semaphore 自身能够控制 running count，一旦被 evnent 被 signal 则所有 waiter 会被唤醒。因此这个实现版本在一些 edge cases 上花了不少功夫。

---

[Implementing a Recursive Mutex](https://preshing.com/20120305/implementing-a-recursive-mutex/)

如何让 mutex 支持 recursive mode

注：实际设计中，尽可能避免使用 recursive lock

---

[Lockless Programming](https://docs.microsoft.com/en-us/windows/desktop/DxTechArts/lockless-programming)

微软官方出品的无锁编程介绍文章。

质量过硬，干货满满。

---

[Memory Reordering Caught in the Act](https://preshing.com/20120515/memory-reordering-caught-in-the-act/)

构造一个触发经典 memory reordering 的场景

## System Architect Design

[哥们，你们的系统架构中为什么要引入消息中间件？](https://mp.weixin.qq.com/s/tiN6Z_VjTKZ5voFu4TFElg)

- 多个组件/服务之间的解耦
- 剥离耗时调用异步化
- 高峰流量作为 buffer 屏障进行流量削峰

---

[Circuit Breaker and Retry](https://medium.com/@trongdan_tran/circuit-breaker-and-retry-64830e71d0f6)

what is circuit breaker and why would we use it

common design patterns of retry

## Misc

[Lightweight In-Memory Logging](https://preshing.com/20120522/lightweight-in-memory-logging/)

一个针对特殊场景涉及的 in-memory logging facility。

尽量减少 logging 产生的指令级别开销（例如尽可能使用 intrinsic 获取相关信息）

---

[Memory Ordering at Compile Time](https://preshing.com/20120625/memory-ordering-at-compile-time/)

How once upon a time does compiler reorder instructions.