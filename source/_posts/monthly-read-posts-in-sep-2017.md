---
title: Monthly Read Posts in Sep 2017
categories: PROGRAMMING
date: 2017-10-01 00:14:27
tags: [c++, android, allocator, http, curl, typedef, smart poiner, covariance]
---
[The Horror of the Standard Library](https://www.zerotier.com/blog/2017-05-05-theleak.shtml)

一个 allocator 引发的惨案...

---

[CppCon2015: Extreme Type Safety with Opaque Typedefs](https://channel9.msdn.com/Events/CPP/CppCon-2015/CPPConD02V011)

类型自带语义，但是 `typedef/using` 只是类型别名，编译器无法区分。而 opaque typedefs 声称可以通过通过 wrapper 的方式生成具有单独类型语义 typedefs。

---

HTTP Series

https://www.code-maze.com/http-series-part-1/
https://www.code-maze.com/http-series-part-2/
https://www.code-maze.com/http-series-part-3/
https://www.code-maze.com/http-series-part-4/
https://www.code-maze.com/http-series-part-5/

可以作为不错的 HTTP 协议扫盲

---

[CppCon2015: C++ Requests - Curl for People](https://www.youtube.com/watch?v=f_D-wD1EmWk)

受到 python requests 启发的 C++ counterpart implementation。

基于 CURL，但是做了非常细致的封装。

看了一下 demo，感觉是个非常不错 curl wrapper

---

[Android GC 那点事儿](https://mp.weixin.qq.com/s?__biz=MzI1MTA1MzM2Nw==&mid=400021278&idx=1&sn=0e971807eb0e9dcc1a81853189a092f3&scene=1&srcid=1019lhTQRdJKb9HCqUHPntbT&from=groupmessage&isappinstalled=0#rd)

行文杂乱，讲道理还不如看专业的 posts...

---

[How to Return a Smart Pointer AND Use Covariance](https://www.fluentcpp.com/2017/09/12/how-to-return-a-smart-pointer-and-use-covariance/)

为 C++ 中实现涉及 smart pointer 的 covariance 接口提供了一个非常不错而且可用的解决方案。
