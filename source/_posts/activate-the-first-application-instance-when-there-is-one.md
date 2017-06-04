---
title: 禁止程序多实例并存并且自动激活第一个实例
categories: PROGRAMMING
date: 2017-06-05 00:00:09
tags: [Windows, multiple instance]
---
多实例检测非常常规，在程序启动时直接检查用来标记的内核对象是否存在即可，一般都是使用 `Mutex`。

麻烦的点在于如何激活第一个实例，显示它的主窗口。

某直播姬一开始的想法是利用 Pipe 建立 IPC 通讯连接，然后后续实例通过发送消息，让主实例 activate 自己的窗口。

嗯，看起来没毛病，但是做起来问题很多。

首先，IPC 建立需要时间，通讯需要时间，并且断开 IPC 后主实例（Windows 下）一定得需要重新创建一个 Pipe；更加诡异的是，我在本地编译的 Rlease 版本明明可以工作，利用构建机编译出来的版本，子实例管道连接一直失败....

于是看起来 IPC 是一个很自然地选择，实际上却是一个导致复杂度暴涨的下策。

另一个思路是创建一个 Event 内核对象，后续实例直接通过 signal 这个 event 内核对象来通知主实例。

但是这里实现起来有个麻烦的地方：如果要追求简单化，那么就需要一个单独的线程 wait 在这个内核对象上，负责接收通知，缺点是要浪费掉一个线程；如果不想资源浪费，那么考虑到很多框架都提供了 async i/o 的 wrapper，所以可以利用这个点，你开心的话可以挂到 IOCP 上。

嗯，等等，我们是想做啥来着？！

绕了一圈最后想想还是这样算了:

利用 `RegisterWindowMessage()` 注册一个自定义消息，主实例在 UI message loop / window message procedure 里监听这个消息；子实例启动后就利用 `BroadcastMessage()` 或者 `PostMessage()` 广播一下这个消息。

这样有个额外的好处，主实例收到消息直接是在 UI 线程，省掉一次 post task。

某直播姬最后采用的就是这个方案，为此我加了一个 class 起了个名字叫 *SingleInstanceGuarantor*，嗯，有点中二的感觉。

最后要注意一点，如果框架封装了 window procedure，将几个活动窗口的 window procedure 聚合到一个 message handler 里的话，可能会收到多条消息，可以利用消息的 timestamp 过滤一下。