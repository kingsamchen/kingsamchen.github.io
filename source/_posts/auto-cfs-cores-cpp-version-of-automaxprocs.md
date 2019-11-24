---
title: auto-cfs-cores C++ 版 automaxprocs
categories: PROGRAMMING
date: 2019-11-24 17:10:41
tags: [cpp, docker, cfs]
---
之前写了一篇分析 uber 的 automaxprocs 的源码，后面抽个了时间写了一个 C++ 版本的。

其一是为了加深对原库的理解；其二是避免太长时间没写 C++ 手生，以及顺带体验一下 C++ 17 的感觉。

代码在：https://github.com/kingsamchen/Eureka/tree/master/auto-cfs-cores

PS：一开始用 filesystem 的时候发现 libstdc++-8 需要专门连接一个 `lstdc++fs`，否则会因为符号不在同一个 lib 里直接 coredump。。。

最后折腾了一圈还是直接用了以前自己写的 `Path`

PPS：`std::optional` 还挺好用
