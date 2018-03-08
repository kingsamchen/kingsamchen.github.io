---
title: Tiny Concurrent Http Server on Windows and Linux
categories: PROGRAMMING
date: 2018-03-08 23:46:41
tags: [windows, linux, iocp, epoll, tcp, concurrent]
---
前段时间趁着春节，分别基于 IOCP 和 epoll 实现了 demo 级别的 http server（在遵守 http 1.1 socket 复用基础上只提供了某个指定目录下文件的 GET），算是简单的过了一下 proactor 和 reactor 模型下的 TCP 并发服务。

功能做的很粗糙，并且没有封装类似 event-loop 的东西，连接管理也基本算是纸糊的，原因还是前面说过的，只是想过一下两种模型，并且，在不研究当前流行的 paradigm 的前提下，凭借自己的 first understanding / hunch 去实现；等对这块有一段时间的研究后，作为参照，来回对比以加深理解。

我觉得这是一种不错的学习方式。

代码可以看 [这里](https://github.com/kingsamchen/Eureka/tree/master/ConcurrentHttpServer)
