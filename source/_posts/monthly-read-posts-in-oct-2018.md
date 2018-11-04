---
title: Monthly Read Posts in Oct 2018
categories: PROGRAMMING
date: 2018-11-04 09:38:36
tags: [c++, epoll, tcp, network, goroutine, RFC]
---
## Concurrency

[C11 Lock-free Stack](https://nullprogram.com/blog/2014/09/02/)

使用C 11 atomic operations 实现一个轻量的 lock-free stack
- pre-allocation，不需要考虑单个节点的销毁问题，所以不需要使用 hazard pointer
- free 和 head 两个列表，两个单独的 head 均可用做 sentinel
- 使用 aba counter 来避免 ABA problem

---

[MySQL/InnoDB中，乐观锁、悲观锁、共享锁、排它锁、行锁、表锁、死锁概念的理解](https://www.souyunku.com/2018/07/30/mysql/)

感觉这篇讲的好混乱，几个概念是明显正交的都混在一起了。

瞄了一眼，基本脑子里都在找平常 locking mechanism 里的概念做映射了。

---

[The method to epoll’s madness](https://medium.com/@copyconstruct/the-method-to-epolls-madness-d9d2d6378642)

总体写的一般，而且内容有点乱。

madness 应该指的就是 epoll 对外表现的是引用 file descriptor 但是内部维护的确实 file descrption。
<!-- more -->
所以最后一个部分放 ET mode 的意图在哪？

---

[A helper template function to wait for WaitOnAddress in a loop](https://blogs.msdn.microsoft.com/oldnewthing/20180118-00/?p=97825)

[A helper template function to wait for a Win32 condition variable in a loop](https://blogs.msdn.microsoft.com/oldnewthing/20180119-00/?p=97845)

Template functions to solve synchronization facitilies that incur spurious wakeup.

## Network

[The Limitations of the Ethernet CRC and TCP/IP checksums for error detection](http://noahdavids.org/self_published/CRC_and_checksum.html)

TCP 和 IP 的 checksum 校验可能会出现 undetectable errors，导致网络层或传输层校验通过但是应用层数据异常。

应用层需要额外的更加强效的校验机制。

---

[When to Use What: REST, GraphQL, Webhooks, & gRPC](https://nordicapis.com/when-to-use-what-rest-graphql-webhooks-grpc/)

几种服务端API技术栈的简单介绍和选择。

---

[TCP_NODELAY: 2018 BEST PRACTICES FOR TCP OPTIMIZATION](https://www.extrahop.com/company/blog/2016/tcp-nodelay-nagle-quickack-best-practices/)

覆盖 Nagle's algorithm 和 delayed ACK

以及一些“经验”是否启用 TCP_NODELAY

基于数据反馈的经验估计要结合一些网关设备来进行。

---

[TCP连接建立的三次握手过程可以携带数据吗？](https://0xffffff.org/2015/04/15/36-The-TCP-three-way-handshake-with-data/)

short anwser: yes

其实我在看 computer networking: a top-down approach 时里面就提到了握手第三次的segment是可以附带数据。

另外有很多 socket 相关的 API 的设计上也可以看出这点。

## Programming Languages

[The Cost of Enabling Exception Handling](https://preshing.com/20110807/the-cost-of-enabling-exception-handling/)

环境 Windows VC（看起来是老版本） 32-bit build
可以看出哪怕是这个环境下，使用（SEH-based）异常的性能损耗也非常小

另外可以参考评论区，Jeff 在 64-bit 下跑出来的数据基本表明了 happy path 下开启异常的开销几乎为0。

---

[value_ptr — The Missing C++ Smart-pointer](https://hackernoon.com/value-ptr-the-missing-c-smart-pointer-1f515664153e)

smart pointer with value-semantics

有点意思的搭配....

---

[goroutine背后的系统知识](http://www.sizeofvoid.net/goroutine-under-the-hood/)

站在系统设计角度思考 goroutine

膜一下作者的深厚功力....

---

[浅析C++多线程内存模型](http://www.parallellabs.com/2011/08/27/c-plus-plus-memory-model/)

可以当作口水文看一下...

## Misc

[普通程序员怎么理解日志系统](http://www.algorithmdog.com/loggingsystem)

从 logging 的角度说，这篇文章只能说凑合，logging的几个点都有了，但是缺乏深度。

个人角度，logging system 四大 aspects：
- severity level，用于控制日志的粒度
- header / context，每条日志消息需要包含的基本上下文信息（severity level, datetime 需要注意 resolution, pid, tid, filename, line .etc），主要用于后期定位辅助或者用于后续系统的分类
- output destination, console 还是 文件，console的话, 选择 stdout 还是 stderr, 文件的话怎么做 rolling
- throughput，保证高 throughput 的优点在于如果目标的 throughput 只有最高值的 x%，那么系统可以腾出 1 - x% 的资源来做正经事，避免 logging 本身成为瓶颈

---

[Why does HRESULT begin with H when it’s not a handle to anything?](https://blogs.msdn.microsoft.com/oldnewthing/20180117-00/?p=97815)

It was indeed an HANDLE, for storing cascading failures; but was finally changed to a integer.

---

[How can I reserve a range of address space and receive notifications when the program first reads or writes a page in the range?](https://blogs.msdn.microsoft.com/oldnewthing/20180124-00/?p=97875)

Short anwser: use `PAGE_GUARD` and install your own exception handler.

Note: Your custom page fault handler will be called only for page faults incurred by user mode.

---

[How to Read an RFC](https://www.mnot.net/blog/2018/07/31/read_rfc)

RFC newbie 福音。

涵盖：
- 从哪里找 RFC 文本
- 如何理解手头这份 RFC 文本的一些基本信息，比如主题、取代的旧版本，对其他主题的影响 .etc
- 阅读 RFC 时注意结合上下文，避免出现断章取义，相关章节最好一起同时阅读
- 对于一些要求词的解释（MUST / MUST NOT / REQUIRED / SHALL / SHOULD .etc）着重讲了 SHOULD 的一些潜规则
- 不要光只看 example，一定要看全完整的 section 内容，避免 example 包含错误被误导了
- 熟悉 ABNF 规则
- 注意安全部份
