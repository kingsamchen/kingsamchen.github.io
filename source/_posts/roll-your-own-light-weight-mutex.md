---
title: 基于 semaphore 实现轻量级 mutex
categories: PROGRAMMING
date: 2019-05-12 20:10:37
tags: [multithreading, synchronization, semaphore, mutex]
---
核心是 Jeff Preshing 大牛的两篇文章

- [Roll Your Own Lightweight Mutex](https://preshing.com/20120226/roll-your-own-lightweight-mutex/)
- [Implementing a Recursive Mutex](https://preshing.com/20120305/implementing-a-recursive-mutex/)

讲述如何利用操作系统（Windows）提供的基础同步原语，例如 semaphore，实现一个轻量级的 mutex 同步原语。

这个 home-made mutex 有如下特点：

- 支持常规的 `lock()`, `unkock()` 和 `try_lock()`
- 在没有 race 的情况下 lock/unlock 操作都是简单的 atomic operations，开销小
- 支持 recursive mode（第二篇文章里阐述）

之所以说是轻量级，是因为这个实现缺少一些高级功能，例如 `CRITICAL_SECTION` 提供的多核环境下阻塞线程前会进入 spinning state；同时在 Linux 等系统下，缺少应对 thread priority inversion 的能力 .etc

在功能够用的情况下，根据 Jeff Preshing 给出的 benchmark，这个 Lightweight mutex 性能不俗。

## 前方大量评注（吐槽）

如果你看完了这两篇文章并且也大致上弄明白了文章里给出的实现代码，可以继续往下看。

**0x00** 事实上，Windows 的 `CRITICAL_SECTION` 也是类似的实现。当然附属功能多了不少就是。

**0x01** 第二篇文章里为了实现 recursive mode，给每个 mutex 增加了一个 owner-thread 的成员。实际上，哪怕是非 recursive 的实现都应该有这个成员：因为 mutex 区别于单纯的 semaphore 的一个核心特点是，它具有 ownership 的概念。

因此第一篇文章里的实现是有欠缺的，会导致线程 A 上的 lock 能被线程 B unlock。

这个行为直接暴露了底层 semaphore 的语义，但不是 mutex 自身的语义。

**0x02** Golang 的 `Mutex` 实现上没有 ownership 的概念，选择了直接暴露了底层的 semaphore 语义。这是一个很迷的设计，GitHub 上有人提了 issue，但是官方给的回复非常的 interesting。

**0x03** 示例代码里，原子的增/减用的是 `_InterlockedIncrement()` 和 `_InterlockedDecrement()`；这两个 intrinsics 会返回更新后的值。所以相比下，使用 `_InterlockedExchangeAdd()` 会更加高效。

因为这三个 intrinsic 函数编译后同样生成 `lock xadd` 指令，但是后者返回的是 original value 指令相比下少一条。

**0x04** `try_lock()` 的实现需要用到 CAS 指令，开销更大，因此在 recursive mode 的实现中，仅当当前线程不是 owning thread 时再去尝试改变 mutex 状态是一个更好的实现。

