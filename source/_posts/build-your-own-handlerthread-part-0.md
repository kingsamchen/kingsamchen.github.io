---
title: Build Your Own HandlerThread Part 0
categories: PROGRAMMING
date: 2018-12-03 23:50:08
tags: [Android, HandlerThread, Looper, Handler, MessageLoop]
---
### 立一个 Flag

`HandlerThread` 是 Android runtime 提供的一种线程基础设施；和 Java Thread 相比，`HandlerThread` 最大的优点是自带消息循环（message-loop），这使得用户可以很方便地基于 `HandlerThread` 实现 CSP ([Communicating Sequential Process](https://en.wikipedia.org/wiki/Communicating_sequential_processes)) 模型，极大降低多线程编程的复杂度。

市面上讲解/分析 `HandlerThread` 内部实现的技术文章已经有很多（虽然恕我直言，这类文章几乎99.99%都是垃圾，毕竟一群连 native 轮子都没摸过的人写的代码分析本质上和太监写的性爱宝典没什么区别），所以我们另辟蹊径，不再重复他人实验，而是从头实现一个自己的 `HandlerThread`。

因此从此篇开始，本系列将会一步步展示如何 build your own handler-thread from sractch；同时为了增加辨识度，同时避免一些可能的版权纠纷，我们换一个另类一点的名字，比如 `ActiveThread`。

既然整个 Android Framework 都是基于 Java 实现，我们这里也基于 Java 实现我们的 `ActiveThread`。

### 组织结构

整个设施在结构上会参考 Android 的 `Looper`, `Handler` 体系，但是具体的设计和名字会有所更改。

同时，随着 Java 8 的逐渐广泛使用，在 Java 中写 lambda 终于不再是一件异常痛苦的事情。因此我们的实现不考虑 Android 体系的 `Message`，只做 `Runnable` 或 `Callable` 的支持。
