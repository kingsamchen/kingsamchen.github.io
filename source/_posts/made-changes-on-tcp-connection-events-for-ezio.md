---
title: 调整 ezio 的 TCPConnection 状态事件
categories: PROGRAMMING
date: 2019-01-27 11:05:48
tags: [ezio, tcp]
---

上上周的时候给 ezio 做了一个[调整](https://github.com/kingsamchen/ezio/commit/d9e7bde28155b5bb2f144dd4c42083d95ef7b674)，稍微修改了一下 `TCPConnection` 对外暴露的几个状态变化的事件。

起因是在写 [example/chat-client](https://github.com/kingsamchen/ezio/blob/9a2ea3ffa43578555e8dfcfe769db10bb45e0087/examples/chat/chat_client.cpp#L62) 的时候，因为主线程单独跑了一个事件循环从 `stdin` 中读取用户输入，所以 `ChatClient` 以及内部的 `TCPClient` 是跑在另外的工作线程上。

因为那个时候 `TCPClient` 之对外暴露了 connection 和 disconnection 的事件回调（这两个事件还统一成了一个 `on_connection()`），所以自然选择在 disconnection 的时候进行退出主循环的操作。

但是这个时候会出现：

- 主循环结束后立马析构 `ChatClient`，连同内部的 `TCPClient` 一起销毁。因为这部分代码跑在主线程上，所以不会和工作线程有任何 coordination。
- `TCPClient` 会在触发 `on_connection()` 来表明连接断开后会继续做一些内部清理工作；然而因为前面已经将 `TCPClient` 析构了，导致 UAF

而当时为了解决这个问题，采用的 workaround 时，`ChatClient::OnConnection()` 在发现连接断开后，通过 `RunTaskAfter()` 的方式延后执行 `EventLoop::Quit()`。

这个做法非常丑陋而且不可靠。
<!-- more -->
**注意：任何在生产环境中需要依靠 delay-execution，比如很多人喜欢的 `sleep()`，`postDelayedTask()` .etc 来实现恰好的同步都是错误的，因为上下文调度的不确定性，这种做法基本就是给自己挖坑。**



### Solution

在花了一点时间做了一个 UML 时序图将 `TCPConnection` 以及对应的 `TCPServer` 和 `TCPClient` 关联之后

![](/img/life_sequence_of_tcp_connecion.svg)

我发现其实我需要的，大概就是增加一个 `TCPConnection` 实例销毁的事件回调。

于是我们就有：

| Event         | Call Context      | Semantcis                                                    |
| ------------- | ----------------- | ------------------------------------------------------------ |
| on_connect    | MakeEstablished() | The connection has been established and each end of hosts **can now send or receive** data via the connection. |
| on_disconnect | HandleClose()     | The connection is closed and no more further data transmission should be done over this connection. |
| on_close      | HandleClose()     | Notify the owner, i.e. TCPServer or TCPClient, to do clean-up for this connection. |
| on_destroy    | MakeTeardown()    | Notify the owner, i.e. TCPServer or TCPClient, this TCPConnection instance is about to be destroyed. |

同时为了更加明确连接和断开的事件语义，我把 `on_connection()` 给拆成了 `on_connect()` 和 `on_disconnect()`。

有了这么一个调整，chat-client 里只需要在 `TCPClient::on_connection_destroy()` 里退出主线程的事件循环就可以了。因为这个时候 `TCPConnection` 已经是一个可以安全被销毁的状态。

这个修改其实也就是上面提交里做的。