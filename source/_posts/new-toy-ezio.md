---
title: 开了个新坑 ezio
categories: PROGRAMMING
date: 2018-08-31 01:04:26
tags: [TCP, epoll, IOCP, network]
---
前段时间分别在 Linux 和 Windows 下实现了一个简单的 TCP 网络框架之后，感觉一些必要的坑都踩的差不多了，可以开始动手写一个真正的跨平台的 TCP 网络框架了。

于是就有了这个新坑：ezio。

ezio 这个名字是某天跑步的时候突然想到的，有几重含义：
- 致敬刺客信条里的 Ezio Auditore，寓意希望这个库能够和刺客一样，虽轻量，但需要时恰到好处，不含糊
- 致敬（碰瓷）Boost.ASIO，因为两个名字发音非常接近
- 第三个是朋友发现的，他把 ezio 念成了 e-z-io（easy io），于是赋予了一个新的含义

ezio 的目标是：**提供同时支持 Linux 和 Windows 的轻量型非阻塞且具备扩展性（non-blocking and scalable）的 TCP 网络框架；针对 Linux 做性能侧优化，针对 Windows 做一致性开发体验的优化。**

这个目标初看起来有点怪异，但是确实非常实际的做法。

因为 Linux 和 Windows 在网络编程上存在根本的范式差异性，所以为了提供一致的对外接口，必须要有所侧重和牺牲。Linux 目前占据了绝大多数互联网产品服务器系统的份额，而 Windows 则提供了可以说是当前最好的开发环境。

ezio 这个设计目标可以使得绝大多数业务逻辑在 Windows 上开发，然后在 Linux 上进行最后的部署。

<!-- more -->

## 一些有点技术范的讨论

**0x00**

宏观上选用的 IO 模型是 one-loop-per-thread，并且一个 master thread 负责 accept 新连接，然后从一堆 worker thread 中选出一个将 socket 分给他。

这个模型的核心是每个线程独有的 event-loop。

在 Linux 上选择的显然是 non-blocking + epoll；在 Windows 上则是 IO completion port + overlapped IO。

**0x01**

因为 non-blocking 本质上仍是同步 IO，而后者则是实打实的异步 IO，所以这里有一个融合的问题；用俗话说就是，reactor pattern 和 proactor pattern 要如何 unify。

我的策略是：将前者转换为后者，比如对于 read 操作，内部 read 完成后才通知用户，并且给到读到的数据。

有人怀疑这样做会有不必要的开销，我觉得这是不对的，因为封装完善的网络框架不应该将对 socket 的 `read()` 或者 `write()` 直接暴露给用户让用户完成，相反，用户接触到的只是框架提供的一层抽象。

如果直接暴露给用户，那么用户应该用 `read(2)` 去读 socket 还是用 `readv()` 呢？如果我们封装 epoll 时选择的是 edge-trigger，那么用户就一定要反复 `read()` 直到返回 `EAGAIN` .etc 这意味着用户不仅需要关心底层 IO multiplexor 的具体实现，还要自己管理应用层的 buffer。

另外，我不觉得 libevent, libuv 甚至 Boost.ASIO 是封装完善的网络框架；相反，在实际工程使用它们避不开需要再往上面加一层不会太薄的封装。

所以不管是 reactor 还是 proactor，这是对直接操作 multiplexor 的上下文来说的，框架用户看到的应该只是完成的通知；而性能的保证，应该落在 application-layer buffer 的实现上。

**0x02**

epoll 虽然是 poll 的后继者，但是在很多地方反而和 IOCP 存在相似之处，而这些地方刚好和 poll 存在差异。

首先，epoll 和 IOCP 都是有状态的（stateful）。

要让 epoll / IOCP 管理一个 fd / handle，首先要将这个 fd / handle 与之关联；之后对这个 socket 的所有 IO 操作就可以直接从 epoll / IOCP 中获取。

这带来两个改变：
- event-loop 自身不需要维护关心的 socket 列表了，因为对应的 multiplexor 内部已经维护了一份
- socket 的生命周期的维护变得更加重要，尤其是 epoll 在设计上犯了一个很愚蠢创新（内部区分 file descriptor 和 file description）

另外，无论是 `epoll_wait()` 还是 `GetQueuedCompletionStatusEx()` 都可以尝试获取指定大小的事件数组；这样的话就可以设计一个统一的优化策略：如果某次大小为 n 的数组用满了，下一轮之前可以将数组大小翻倍（或者 1.5x 放大）。

**0x03**

虽然理论上异步 IO 的性能要高于非阻塞同步 IO，但是异步 IO 割裂了上下文，尤其是资源上下文，因此在资源管理上充满了艰险。

首先为了让 os kernel 能够直接将网卡收到的数据写入内存页，这部分内存必须事先提供；而操作系统避免内存页被内存管理 swap-out，必须将这块内存页 pin 住。

对于每个发出去的 read 异步请求，都伴随着一块被 PIN 住的 mem-page，更揪心的是在请求 complete 之前，内存页始终被 pin 的死死的。

如果有很多恶意连接，建立了连接之后就什么都不做，那么按照每个内存页 4K 计算，用不了多少时间内存管理就撑不住了。在 Windows 上这个时候异步的 IO 操作就会返回一个错误 `WSAENOBUFS`，表示 too many pinned memory pages。

对于这个问题，我的想法是使用 read-probe-request，即对于新建立的连接，先发一个大小为 0 的异步 read，如果之后这个请求完成了，那就说明 socket 存在可读数据，后面就可以大胆读了。

当然，不排除有恶意客户端针对性的搞破坏，那样就要做一些资源使用的处理了。

对于 accept 调用来说，这个问题稍微没有那么严重，因为可以控制 concurrent outstanding accept requests 的数量在一个比较小的值，比如 10；甚至完全不考虑做优化，顺序依次的发都行，毕竟这个优化对应到 epoll 也要求调用 `accept()` 直到出现 `EAGAIN`，并不是一个每个人都会做的优化。

相比较下，同步 IO 一个明显的优势是可以利用 stack，内存资源的使用上更加紧凑。

这个很容易理解，因为 `read()` 操作的发起和结束是在一个函数调用上下文，完全可以在 `read()` 调用时使用一部分 stack 空间，比如 64-KB，利用 `readv()` 读取一个 buffer vetor 即可。等到这个栈帧结束，这部分资源完璧归赵。

对于异步 IO 来说，因为 request 和 completion 是分开的上下文，栈内存就别想了，只能动一些其他的手段。

**0x04**

level-trigger 还是 edge-trigger，这是使用 epoll 时首先需要考虑的，毕竟这影响到内部的 IO 策略。

结合之前的经历，我觉得选择 level-trigger 是一个比较稳妥地做法。

对于读侧来说，如果使用 LT，那么每次能读多少就读多少，反正只要还能读，下一轮 polling 还有机会。而对于 ET 来说，则需要反复调用 `read()` 直到出现 `EAGAIN`，哪怕你某一次读到的数据小于 buffer size。

`read()` 毕竟是 syscall 啊，多几次调用的开销是不能忽视的，而且一般来说，如果 size-read 已经小于 buffer，那么有很大的概率下一次 read 几乎一定会返回 `EAGAIN`，这是一个令人失望的故事。

对于写操作来说，如果使用 LT，那么需要注意，一旦 application-layer output-buffer 空了，那么就要立马屏蔽这个 socket 的写标志，避免上层没有书需要写，但是 socket 始终可写导致出现 busy-loop。

如果使用 ET，这点稍微好一些，但是 epoll 不支持对 read 和 write 分开设置 LT / ET 不是...

综上，如果使用 LT，那么要注意避免 busy-loop；使用 ET，需要注意避免漏了活动事件，而反复直到确认 `EAGAIN` 出现也有开销。

所以 ET 不一定就性能高，反而使用 ET 需要加倍小心，更具心智负担。

可能除了一些极端的需要 micro-optimization 的场合，LT 都是首选。

**0x05**

多线程环境。

在多线程环境中实现并发 TCP 网络编程可以说是荆棘密布，一不小心就能摔倒坑里。

区别于常规的 disk IO，TCP socket IO 存在本质的特点，那就是存在 short-read 和 short-write。

因此在多线程环境中，首要做的就是**避免存在多个线程能操作同一个 socket 的情况**。

实话说，在17年底我还没正式系统学习过 TCP 网络编程时，写的两个（分别针对 Windows IOCP 和 Linux epoll） tiny http server 就犯过这样的错误。

epoll 本身是没有明确说一个 epoll-fd 是 thread-safe 的， ET + EPOLLONESHOT 是一个解决方案，但是看起来更像是掩耳盗铃，而且一次性作对的难度不小。

具体细节可以参考我之前在 montly read posts 里分享过的这篇文章：[epoll is fundamentally broken](https://idea.popcount.org/2017-02-20-epoll-is-fundamentally-broken-12/)

对于 IOCP，虽然微软明确说 IOCP 是 thread-safe 的，并且内部设计时也考虑了多个线程访问同一个 device-handle 的负载均衡（这样就允许在操作中使用 blocking functions），但是必须要认识到一点：IOCP 也是支持 disk I/O 的！

对于 disk I/O 来说，这么做时无可厚非的，但是 TCP socket IO 却是存在 short-read 和 short-write。

假设两个线程同时对一个 socket 做了 read 请求，那么首先回来触发应用层回调的一定先发请求的那个线程吗？考虑过期间系统调度的影响吗？

在 CodeProject 上能看到不少这样的文章：为了解决乱序的问题，对每个请求进行 seq 的编号，同时考虑到多个线程访问同一个 buffer 存在 race 又要上锁...

这不就是重新发明了一遍 TCP sliding window protocol 吗...

如果保证一个 socket 只有一个线程操作，那么什么事儿都没了；而这个，才是 one-loop-per-thread 所要表达的真正的意思。

当然，eventloop-thread 中要避免出现 blocking 操作，这样刚好有可以和 Linux epoll 保持一致了。

**0x06**

想了一下，觉得关闭逻辑也有必要提一下。

对于服务端程序来说，应该尽量避免主动 shutdown；主要是为了避免进入 TIME-WAIT，毕竟这个时间段长达 2MSL（据说在 Linux 上固定是 60s)。

那么正确的关闭逻辑应该这样操作：
- 服务端通过 `EPOLLRDHUP` 或者 `read()` 知道对端关闭，然后进入被动关闭流程。
- 如果服务端自己要结束，则服务端通过 `shutdown()` 先关闭 socket 的写端，保留读端（因为可能有数据还在路上）；这样的客户端通过 `EPOLLRDHUP` 或者 `read()` 返回 0 就主动断开连接，发出 FIN 包；然后服务端接着进入被动关闭流程。

对于一些异常的 socket，服务端别无法他，只能主动断开，一般来说这种关闭的连接数量较少。

## 结语

哔哔了这么多，希望能在十月份结束前完成整个库的实现。嗯，最起码别烂尾...