---
title: Monthly Read Posts in Mar 2018
categories: PROGRAMMING
date: 2018-04-01 12:03:00
tags: [concurrency, lock-free, constexpr, actor, coroutine, c10k, pthread, technical debt]
---
[Sorting, caching and concurrency](http://akumuli.org/akumuli/2015/03/19/sequencer/)

通过引入基于时间的 sliding window 来标记并剔除（大概率）由错误产生的数据；并且可以利用 SW 来 schedule 数据什么时候从内存持久化到磁盘。

Patience sort 是 merge-sort 的一种变体，适合对 slightly unordered data 进行排序，最坏情况下退化为 merge-sort。

实现 optimistic locking 来避免使用 reader-writer locks，进而防止写饥饿。

> 引入一个原子的 sequence-number，初始状态为0；并且
> - merge 操作开始时，sequence-number 自增
> - merge 操作结束时，sequence-number 同样自增
> 于是利用 sequence-number 的奇偶性来区分某个操作是否结束
>
> 如果写操作开始但是未完成，则读操作进行退避&重试。

感觉作者这么做应该是结合了业务的上下文；这个 optimistic locking 的策略至少有这么几个前提：
1. 写操作很重要，不能有明显的延迟
2. 写操作不会太慢，至少不会导致读操作的延迟超过无法承受的范围
3. 平台提供的 reader-writer locks 对 writer starvation 的防治策略做的不够好

---

[An Introduction to Lock-Free Programming](http://preshing.com/20120612/an-introduction-to-lock-free-programming/)

非常赞的 Lock-free 入门文章。

覆盖了 concepts, CAS operations, memory ordering & memory models

注：作者 preshing 是个 concurrency programming 领域的大触

---

[Gentle Introduction to Lockless Concurrency](https://getpocket.com/a/read/994738352)

同样也是一篇 lock-free 的入门文章，但是这这篇文章比起上一篇就差太多了，明显不是一个层次的。

这篇是基于 Java 来谈 lockless，涉及的点明显局限在了语言自身，没有 preshing 那种站在 bare-metal 角度的犀利。

注：原文找不到了，大概率被作者自己删了，因此只有 pocket 的备份

---

[The actor model in 10 minutes](https://www.brianstorti.com/the-actor-model/)

一篇非常赞的 actor model 的介绍文章。

直接开篇就把模型的本质是 actor，以及 actor 的核心（primitive unit for computation & message passing）讲明了。

然后延伸到 fault tolerance 和 distribution 上的优势。

内容比 *7 Concurrency Models in 7 Weeks* 要好多了，讽刺的是这本书还是这篇文章的 reference 之一。

---

[CppCon 2015: Gor Nishanov “C++ Coroutines - a negative overhead abstraction"](https://www.youtube.com/watch?v=_fu0gx-xseY)

总结起来就是：
- 我们其实很早就开始应用 coroutines 了
- Concurrency TS 里提供的 CPS 也不是万能药
- Coroutine is good, and We want coroutines!

---

[CppCon 2015: Artur Laksberg “Concurrency TS Editor's Report”](https://www.youtube.com/watch?v=mPxIegd9J3w)

Brief introduction to concurrency ts.

涵盖了三点：
- better std::future (with continutation & async/await)
- latch & barrier
- atomic shared_ptr

---

[CppCon 2015: Scott Schurr “constexpr: Introduction”](https://www.youtube.com/watch?v=fZjYCQ8dzTc)
[CppCon 2015: Scott Schurr “constexpr: Applications"](https://www.youtube.com/watch?v=qO-9yiAOQqc)

第一篇是 `constexpr` 入门介绍。

演讲者挺有意思。另外最后面利用 unresolved symbol 来 force as compile time error 有点意思

第二篇，重点来了，干货十足，compile-time containers 可以研究好一会儿。

这下基本能明白为什么这哥们要开两个 talk 了，因为一个 talk 这么多内容说不玩啊。

---

[Please Don’t Rely on Memory Barriers for Synchronization!](http://www.thinkingparallel.com/2007/02/19/please-dont-rely-on-memory-barriers-for-synchronization/)

文章实际上是一片批判文，但是被批判的文章被作者删除了（估计是被喷得太惨了），所以不能拿来做交叉对比。。

文章核心是在强调原则性的做法，但是因为主要是为了批评，所以没有太多的干活，总结一下就是：
- 多看书，不要当民科，自己**发明**轮子
- 如无必要，不要使用 memory barrier 这种过于底层且没有移植性的东西
- 锁不慢，这篇文章很容易给人错的观念
- 先写对，再写快（老生常谈）

---

[Overview of syslog](https://www.gnu.org/software/libc/manual/html_node/Overview-of-Syslog.html)

（Unix/Linux）系统提供的日志存储仓，对应的 daemon 叫 syslogd，使用的 Unix Domain Socket 名是 `/dev/log`。

程序可以使用 syslog.h 文件中的 `syslog()` 或 `vsyslog()` 函数往 syslog 写消息，一条消息最重要的有两部分：
- facility，表明谁发的消息
- priority，消息的重要程度

---

[C++ Metaprogramming - Fedor Pikus - CppCon 2015](https://www.youtube.com/watch?v=CZi6QqZSbFg)

[C++ metaprogramming- a paradigm shift - Louis Dionne - CppCon 2015](https://www.youtube.com/watch?v=cg1wOINjV9U)

两个和 C++ TMP 有关的 talk。

前者是 introduction 性质的，后者嘛。。。其实就是 Boost.Hana 的宣传广告

---

[Java替代C语言的可能性](http://blog.csdn.net/myan/article/details/1482614)

作者（当时）认为这不存在可能性，总结了三个原因
1. Java 程序员的平局水平比起 C 程序员的平均水平还有明显差距，尤其对底层、结构体系的理解
2. 内存占用过高，带来一系列的性能问题，导致目前（当时）且一段时间内只能做上层应用
3. 思维方式的问题，Java 程序员被厚重的框架束缚思维

虽然这是一篇10年前的文章，但是不得不说作者眼光独到。

现在这三个问题依旧，而且目前 Java 在互联网公司的主战场主要是 Android 开发（受到 Kotlin 挑战）和服务端（中间件，上层 web 服务，受到 Go 冲击），确实还未侵入 C 的核心领域。

不过个人感觉，未来编程面对的抽象层次会越来越高。

---

[垃圾收集机制(Garbage Collection)批判](https://blog.csdn.net/myan/article/details/1906)

作者（当时）吐槽主要来自于：繁忙时刻且内存没有明显压力时不会触发 GC，而当内存有明显压力感知的时候，一次 GC 会导致极大的开销。

---

[USING NON-BLOCKING AND ASYNCHRONOUS I/O (CK10 PROBLEM) IN LINUX AND WINDOWS (WITH EPOOL, IOCP, LIBEVENT/LIBEV/LIBUV/BOOST.ASIO AND LIBRT/LIBAIO)](https://coelhorjc.wordpress.com/2014/12/18/using-non-blocking-and-asynchronous-io-ck10-problem-in-linux-and-windows-with-epool-iocp-aiolibaio-libeventlibevlibuv-boost-asio/)

C10K 问题下的各种解决方案。

从基础的 reactor（epoll / kqueue）到 proactor （IOCP）

同时涉及当前流行的网络框架：libev, libeio .etc

适合技术选型或者了解各平台下实现高性能并发处理的方式

---

[Reactor vs. Proactor: Part 1 – The Reactor](http://www.sean-bollin.com/2017/05/01/reactor-vs-proactor-part-1-the-reactor/)

PART 1 简单给予 epoll 做了几个例子。

例子代码写得不好，甚至还出现了利用异常做控制流...

还好作者太监了没有后续。

---

[Designing C++ functions to write/save to any storage mechanism](http://insanecoding.blogspot.co.uk/2013/04/designing-c-functions-to-writesave-to.html)

这篇文章写于 2011 年，这个背景很重要。

在进入 C++ 11 的时代前，相当一部分 C++ 程序员还是**只适应**通过 OO 去构造抽象；而本身就是设计反面教科书的 ostream 体系让相当一部分人在错误的道路上越走越远。

作者吐槽的就是这点。

后文给出的 solution 说穿了其实就是，利用 template 进行依赖注入。

所有写操作，无论是写文件还是写 DB 还是写网络，核心都基于一个具体的 function object，而这个 function object 可以通过 template parameter 进行依赖解耦。

C++ 11 引入的 `std::bind()`、 lambda、甚至 `std::function` 都更加明确了条路子。

这篇文章有点类似孟岩曾经写的利用 bind + function 去避免继承（运行时多态）的滥用。

---

[POSIX Threads Programming](https://computing.llnl.gov/tutorials/pthreads/)

Pthreads 简明教程。

覆盖
- threads VS process (Why using threads)
- pthreads 介绍
- pthreads 创建/销毁
- mutex
- condition variable

可以作为入门级读物。

---

[Copying code != Copying implementation](http://insanecoding.blogspot.co.uk/2014/05/copying-code-copying-implementation.html)

一开始我还以为是作者专门写文章解释他为什么会 copy code，看完才发现不是这样。

作者的观点是：（遵守 license）从开源项目里抄代码直接用是不靠谱的，一定要注意，代码所处的上下文，搞清楚是不是有什么 prerequisites 或者 hidden requirements。

中间还专门举了几个例子。

---

[Visualising and Prioritizing Technical Debt](https://medium.com/@AikoPath/visualising-and-prioritizing-technical-debt-afc82e542681)

核心：将技术债具象化，无论是采用四象限法还是技术债树。
