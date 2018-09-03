---
title: Monthly Read Posts in July 2018
categories: PROGRAMMING
date: 2018-08-02 22:03:51
tags: [date time, pattern matching, epoll, atomic, multithreading, type traits, constexpr, TCP]
---
## Programming Language

[Clearer interfaces with optional<T>](https://www.fluentcpp.com/2016/11/24/clearer-interfaces-with-optionalt/)

[Partial queries with optional<T>](https://www.fluentcpp.com/2016/12/01/partial-queries-with-optionalt/)

`optional<T>` 介绍 & 简单使用例子

---

[CppCon 2015: John R. Bandela "Simple, Extensible Pattern Matching in C++14"](https://www.youtube.com/watch?v=9IVCVSwn-fI)

一个轻量级的 pattern matching 库简要介绍。

---

[CppCon 2015: Greg Miller “Time Programming Fundamentals"](https://www.youtube.com/watch?v=2rnIHsqABfM)

Google 内部实现的一个处理 date-time 的轮子。

不过讲道理，这块内容还不如关注一下之前 monthly read posts 里出现的[这篇](http://kingsamchen.github.io/2017/08/01/monthly-read-posts-in-july-2017/) 里提到的 date library。

看起来差不多会在 [C++ 20 引入](https://en.cppreference.com/w/cpp/chrono)

---

[When MSDN says NULL, is it okay to use nullptr?](https://blogs.msdn.microsoft.com/oldnewthing/20180307-00/?p=98175)

Short anwser: YES.

<!-- more -->

---
[CppCon 2015:Marshall Clow “Type Traits - what are they and why should I use them?"](https://www.youtube.com/watch?v=VvbTP_k_Df4)

A breif introduction to type traits.

---

[Parsing strings at compile-time — Part I](https://akrzemi1.wordpress.com/2011/05/11/parsing-strings-at-compile-time-part-i/)

[Parsing strings at compile-time — Part II](https://akrzemi1.wordpress.com/2011/05/20/parsing-strings-at-compile-time-part-ii/)

核心是如何利用 `constexpr` functions 实现一些编译期的字符串处理。

PART I 实现了一个括号平衡的检查器；PART II实现了一个四则运算的eval

由于文章写的时间比较早，那会儿 `constexpr` 限制比较大，因此很多代码表达起来比较麻烦。自从 c++ 14 开始放送规则之后，这部分的实现基本成了算法层面。

## Operating System

[When FFI Function Calls Beat Native C](http://nullprogram.com/blog/2018/05/27/)

ELF 文件格式的 PIC 特性会导致 FFI 的性能损失。

动态加载可以一定程度上绕过 PIC 带来的开销

---

[If I call GetExitCodeThread for a thread that I know for sure has exited, why does it still say STILL_ACTIVE?](https://blogs.msdn.microsoft.com/oldnewthing/20180302-00/?p=98145)

核心观点：
1. 函数 `_beginthread()` 返回的 HANDLE 除了用来判断函数是否成功执行外没有任何可靠的作用。
2. 使用 `_beginthreadex()` 可以解决返回的 HANDLE 不可靠的问题。
3. 用 API 前要完整的看完 MSDN 的描述（来自 Raymond Chen 的嘲讽）

---

[The Windows Heap Is Slow When Launched from the Debugger](http://preshing.com/20110717/the-windows-heap-is-slow-when-launched-from-the-debugger/)

在 VS 调试器中运行程序会让程序跑的比直接运行慢，即使跑的是 Release 构建。

原因：被调试器启动的程序会使用 system debug heap，系统在程序使用 `Heap*` API 时做一些错误检测。并且这个 system debug heap 是一个 runtime setting，由操作系统控制（ntdll.dll）控制。

解决方案：
1. 不要在调试器中运行程序
2. 如果（1）无法做到，则定义程序自己的环境变量 `_NO_DEBUG_HEAP=1`

# Concurrent Programming

[Epoll is fundamentally broken 1/2](https://idea.popcount.org/2017-02-20-epoll-is-fundamentally-broken-12/)

[Epoll is fundamentally broken 2/2](https://idea.popcount.org/2017-03-20-epoll-is-fundamentally-broken-22/)

两篇很有意思的posts，探讨了 epoll 两个重大缺陷：

1. 难以跨线程使用注册到 epoll 的 fd
2. 注册的 IO 事件并非和 fd 绑定，而是内部的 file description 内核对象

第一个问题会影响程序的 scalability，细分一下就是**没法简单地**对 `accept()` 和 `read()` 做 load balancing。

第二个问题是纯粹的设计缺陷。

这两个缺陷导致很难正确用对 epoll。

个人备注：epoll 相比 IOCP（没用过kqueue不讨论）确实更加容易使用出错，因此其实需要的是一套合理的方法论。

第一篇 post 里提到的几个问题其实可以通过设计规避，对于一些非通用的服务器程序来说，需要承受性能损失并不大。

---

[多线程程序中操作的原子性](http://www.parallellabs.com/2010/04/15/atomic-operation-in-multithreaded-application/)

入门推荐。

里面有一个容易疏忽的点：对 bitfield 来说，同一个 memory location 的多个 fields必须要用一把锁来保护。

---

[多线程队列的算法优化](http://www.parallellabs.com/2010/10/25/practical-concurrent-queue-algorithm/)

一个 node-based blocking queue 的优化实现。

通过两个单独的锁分别保护 enqueue 和 dequeue 操作。

## Server-End Programming

[Comparing API Gateway Performances: NGINX vs. ZUUL vs. Spring Cloud Gateway vs. Linkerd]( https://engineering.opsgenie.com/comparing-api-gateway-performances-nginx-vs-zuul-vs-spring-cloud-gateway-vs-linkerd-b2cc59c65369)

几个开源 API Gateway 的性能评测

---

[TCP is an underspecified two-node consensus algorithm and what that means for your proxies](https://morsmachine.dk/tcp-consensus)

这篇文章提出的观点有点意思。

作者首先认为，TCP协议其实本质上是两个结点之间的一个 consensus algorithm，这也意味着每个节点有自己 state 需要维护。

后面又指出：因为一开始设计的问题，state 是每个节点独立调整的。如果在两个点之间插一个中间件，那么两个节点的 state negotiation 会受到影响。

比如 L3 的 load balancer proxies 可能会导致 TCP 自身的 keep-alive 失效。

针对协议实现侧：
- 协议应该是 proxy-aware
- 应用层心跳

如果要实现一个负载均衡的 proxy：
- 最好能够实现应用层协议
- 备选方案：实现成一个 NAT，只做 IP packets 的转发

## Misc

[Finding Bottlenecks by Random Breaking](http://preshing.com/20110723/finding-bottlenecks-by-random-breaking/)

介绍了一种找性能瓶颈的方式。

所谓 random breaking，就是程序执行过程中随机的 pause debugger，然后观察 callstack。