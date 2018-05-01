---
title: Monthly Read Post in Apr 2018
categories: PROGRAMMING
date: 2018-05-01 17:50:11
tags: [load balancing, c++, random, cache coherency, consistent hashing]
---
[What is Load Balancing?](https://www.digitalocean.com/community/tutorials/what-is-load-balancing)

Digital Ocean 出品的良心文章，覆盖了：
- what is load balancing
- Why load balancing
- Traffic forwarding
- How does load balancer choose backend servers
- Avoid SPOF of load balancer itself

---

[Introduction to modern network load balancing and proxying](https://blog.envoyproxy.io/introduction-to-modern-network-load-balancing-and-proxying-a57f6ff80236)

非常详尽的一篇介绍 load balancing 的文章，前一篇的所有内容这篇都有，同时还做了一些深入的分析。

例如：L4/L7 的区别；基于 lb 的 service discovery .etc

尤其后面一大块讲网络拓扑的部分，没有实际经验的人表示看起来很痛苦。

---

[CppCon 2015: Gabriel Dos Reis “Contracts for Dependable C++"](https://www.youtube.com/watch?v=Hjz1eBx91g8)

核心围绕着 interfaces as contract 来展开，针对三点：

- precondition
- invariants
- postcondition

展开。

同时提到了一些（想法）构建 programming by contract 的基础设施，例如通过 attribute 来增强语义 blahblah

另，talk 有点无聊，看看 slider 就差不多了。

---

[Dealing with randomness](http://insanecoding.blogspot.co.uk/2014/05/dealing-with-randomness.html)

文章前半部份批判了很多不专业的现象，openssl 再次躺枪。

后面提到了如果要做 cryptographic safe randomness，需要哪些东西。

最后批判了一下随机数去取模这种做法。

但是全文看半天也没有一个操作性很强的指导意见....

---

[A good idea with bad usage: /dev/urandom](http://insanecoding.blogspot.co.uk/2014/05/a-good-idea-with-bad-usage-devurandom.html)

包含 dev/random 和 /dev/urandom 的区别，以及：

要从 /dev/urandom 中读取数据作为密码学安全的初始化种子应该如何正确的实现。

---

[浅论Lock 与X86 Cache 一致性](https://zhuanlan.zhihu.com/p/24146167)

Modern CAS implementation under the hood.

稍稍覆盖了一下 MESI/MESIF 协议。

但是总体来说感觉质量一般

---

[CppCon 2015: Andrei Alexandrescu “Declarative Control Flow"](https://www.youtube.com/watch?v=WjTrfoiB0MQ)

这个 Talk 质量很高，核心就是：如何利用基础的 ScopeGuard 构建出 `ON_SCOPE_EXIT`，`ON_SCOPE_FAIL` 和 `ON_SCOPE_SUCCESS` 语义，并自动执行相应代码。

RAII 这个特质在某个抽象角度上就是标题所说的 it is declarative

这几个东西基本已经作为了 facebook 基础库 Folly 的一部分。

最后提一下，使用 exception 作为 error reporting & handling 机制还是很苦难的，需要依赖很多语言设施（RAII在这里得天独厚），根据语言提供的特性不同会有不同的坑，同时对使用者要求很高。

目前看起来未来新语言使用异常作为核心错误处理机制的应该会越来越少。

---

[Memory management in C and auto allocating sprintf() - asprintf()](http://insanecoding.blogspot.co.uk/2014/06/memory-management-in-c-and-auto.html)

文章分两部分：

第一部分叙述如何“正确”使用 `malloc` 和 `free`，核心包含
- 需要处理 `malloc` 失败返回 NULL 的情况
- 使用 `free` 释放内存之后应该把对应的 ptr 置 NULL，同时建议通过提供一个类似 `insane_free` 的宏来保证

实话说这部分观点差不多仅适合使用 C 的情况，并且需要自己处理内存分配失败在大多数应用系统上显得过于 overkiil。

C++ 在这点做的就比较好，直接抛出一个 `bad_alloc` 异常免得你有什么想法，不过我还是见过很多 catch bad_alloc 的代码。。。；chromium 的做法就比较激进了，在 Windows 上直接设置堆管理器，一旦内存分配失败就立马结束进程。

第二部分是阐述，如何实现一个跨平台的 `asnprintf()`，格式化字符串时不需要外部手动设置 buffer 大小。

这部分会涉及不少不同平台的 runtime 实现的差异性。

---

[Programmer’s Toolbox Part 3: Consistent Hashing](http://www.tomkleinpeter.com/2008/03/17/programmers-toolbox-part-3-consistent-hashing/)

[Consistent Hashing](http://www.tom-e-white.com/2007/11/consistent-hashing.html)

两篇非常棒的讲解 consistent hashing 的文章，第一篇侧重概念和意义，第二篇侧重实践，并且还包含了一个简单的实现。

---

[Improving load balancing with a new consistent-hashing algorithm](
https://medium.com/vimeo-engineering-blog/improving-load-balancing-with-a-new-consistent-hashing-algorithm-9f1bd75709ed)

一种结合 consistent hashing 和 least-connection 的负载均衡算法。

---

How to read in a file in C++ [1](http://insanecoding.blogspot.co.uk/2011/11/how-to-read-in-file-in-c.html) & [2](http://insanecoding.blogspot.co.uk/2011/11/reading-in-entire-file-at-once-in-c.html)

作者比较了几种从文件读取内容的方式（C file IO, C++ fstream, iterator .etc)以及对应的性能指标。

针对同一个编译器的结果排名可以参考；跨跨编译器甚至随着编译器的优化可能结果会有不同，如果需要压榨性能最好还是做一个具体的 benchmark

---
[Message Only Windows](https://bitmazing.com/bcblog/index.php?post/2018/04/13/Message-Only-Windows)

message-only window 可以用来接受各种 windows 事件消息，作为 UI 消息泵使用。

局限性：
- Not visible
- No z-order
- Cannot be enumerated
- Does not receive broadcast messages

---
[CppCon 2015: Detlef Vollmann “Executors for C++ - A Long Story ..."](https://www.youtube.com/watch?v=sAJGoHN6Xx8)

Executor proposal for C++.

实话说这个 Talk 我感觉效果不好。主题不明确，听起来有点云里雾里莫名其妙；演讲者口音也是捉急...