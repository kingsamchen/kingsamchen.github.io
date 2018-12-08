---
title: 放弃给 ezio 加 SIGINT Handler
categories: CODE-LIFE
date: 2018-12-09 00:18:14
tags: [ezio, sigint, signal, Windows, Linux]
---
原本的计划是今天给 [ezio](https://github.com/kingsamchen/ezio) 加上 SIGINT 的处理：自动退出运行的 `EventLoop`，让程序自主正常退出；但是在实现 Windows 版本的过程中发现了一些问题，最后思考再三，决定放弃整个特性。

在 Linux 上，这个实现很简单：添加一个 class `SigIntHandler`，在构造函数中利用 `signal(SIGINT, handler)` 安装自定义的处理函数，然后将这个类作为 `IOServiceContext` 的一个成员就行。

而 Windows 下可以用 `SetConsoleCtrlEvent()` 安装 handler，然后处理对应的 Ctrl+C 事件。

但是在 Windows 下实现是遇到了一点问题：测试过程中我发现退出一直没效果。

退出的代码如下：

```cpp
void QuitEventLoop()
{
    // Acquire from TLS
    auto loop = EventLoop::current();
    if (loop) {
        loop->Quit();
    }
}
```

挂上调试器后我发现两个事实：
- loop 是 `nullptr`
- 执行这个函数的线程是一个**新线程**

第二点可以解释第一点。

查了一下 MSDN 发现这是 by-design：`SetConsoleCtrlEvent()` 永远是运行在一个独立线程。

Linux 下因为 signal-handler 的调用都是在主线程，所以可以拿到主线程 TLS 里的 event-loop。

Windows 这么设计是因为他们觉得 Unix / Linux 的 signal 机制太危险，容易出问题（多线程不友好不说，还独立引入了 async signal-safety 的概念）。详情可以参考 Raymond Chen 写的[这篇](https://blogs.msdn.microsoft.com/oldnewthing/20080728-00/?p=21453)。

那么，要在 Windows 上实现类似的效果，必须要能够拿到主线程的 `EvenetLoop`，我思前想后，除了引入一个 main event-loop 的概念之外，很难保持当前抽象框架上解决这个问题。

同时为可考虑到两个平台的基本行为的一致性，最终我放弃了这个特性。

毕竟，在服务器上跑着的服务，一般都是由类似 `supervisor` 驱动并且长期运行在后台的 XD。
