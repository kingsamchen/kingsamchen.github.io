---
title: Non-blocking Connect(2) and Error Handling
categories: PROGRAMMING
date: 2018-10-27 22:56:18
tags: [linux, non-blocking, epoll, connect]
---
这是我在实现 [ezio connector](https://github.com/kingsamchen/ezio/blob/master/ezio/connector_posix.cpp) 时遇到的一个比较有意（keng）思（die）的问题。

在使用 non-blocking 的 `connect(2)` 时，按照 manual 的[说法](https://linux.die.net/man/2/connect)，如果调用的返回被认为是合理的（例如 `EINPROGRESS`），那么就需要：
1. 将 sock fd 扔到 IO multiplexor 里，然后等待 writable 事件返回
2. writable 返回后检查 `SO_ERROR`，确认没错后才可以认为连接建立

然而因为 ezio 使用 `notifier` 来实现对某个 fd 的 IO 事件侦听和分发，在上面的操作中，除了 writable 事件外，默认还会加上对错误事件的侦听，即 `EPOLLERROR`，并且 event handling 的逻辑中，始终先检查 IO 事件是否包含了错误事件。

在某个 test case 中发现如下情况，整理了一下顺序大概这样：
1. 对一个无 server listening 的地址发起 connect
2. 此时会因为 `EINPROGRESS` 的返回而建立 notifier，并且 notifier 绑定了 `HandleNewConnection()` 和 `HandleError()` 来分别处理 `EPOLLOUT` 和 `EPOLLERROR`
3. epoll 很迅速的返回，然而返回事件里同时包含了 `EPOLLERROR` 和 `EPOLLOUT`，导致先执行了 `HandleError()` 进行了错误处理，重置了 Connector 的某些状态；然后又执行了 `HandleNewConnection()`，触发了这个 handler 里的某些 precondition assertions，直接挂了程序。

这个情况是我事先压根没考虑过的，毕竟到现在也就 `connect(2)` 里会出现 `HandleError()` 和 `HandleNewConnection()` 里包含部分一模一样的错误处理代码。

而且更精彩的是，在我的 WSL (ubuntu 16.04) 里同一个 test case 的代码根本不会触发 `EPOLLERROR`，只有 `EPOLLOUT`；于是错误处理直接留到了 `HandleNewConnection()` 里检查 `SO_ERROR` 的部分，剩下的也是如丝般顺滑。

最后的解决方案是修改了一下两个 handler 的语义，对 `HandleNewConnection()` 做了 error-already-handled-aware 的语义调整。

另外需要提一下日志系统的重要性。

在这个问题的 debugging 过程中，虽然触发了我的 `ENSURE(CHECK)`，有错误的上下文，也完整的调用栈，但是相关信息里根本看不到 `HandleError()` 被调用的痕迹；而且因为需要错误处理的点甚多，如果没有完善的 callstack + logging 机制，很难才能发现是因为 IO event 包含了 `EPOLLERROR`，导致 notifier 调用了 on-error-handler 导致的。

最后吐槽一下，server 端来说 asynchronous 比 non-blocking 难写，但是对于 client 端，asynchronous 反而比 non-blocking 顺滑多了；一个 `ConnectEx()` 丢出去，定时检查一下结果就行了，简直比海飞丝还顺滑。
