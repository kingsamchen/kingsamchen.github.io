---
title: 一周杂记 in Week 3 Sep 2023
categories: CODE-LIFE
date: 2023-09-25 21:02:38
tags: [杂记]
---
本周（09/18 ~ 09/24）是9月第三周距离十一长假就剩下下周最后一周了。😁

## Life

\#1

这周看了两部电影

- 活死人黎明 5/5 给这么高分是因为很符合我的口味，而且叙事上没有太大坑点，最后结局甚至是亮点
- 唐皇游地府 3.5/5 有点逗逼文艺

\#2

因为公司发的鼠标在办公室老是遇到信号干扰一类的因素，导致用起来总是胡乱漂移，非常影响使用心情，所以在刀老师(@Lieo)的推荐下买了巨硬的 sculpture 鼠标

买到后奇怪怎么没有接收器，刀老师教育到说这是直接蓝牙连接的，我这才恍然大悟...

整体体验感觉不错，就是按键略微有点硬，这个需要适应一下。不过鼠标不挑介质表面，而且也没有再出现过漂移的情况了。

打算以后回老家也用这个鼠标 🤣

\#3

这周终于下定决定报了驾校。

周日一大早就起来教练接车跑去医院做了体检和报名缴费。

后面就是先上理论网课把科一考了才能练习实车操作

## Work

\#1

本周学习进度

**CppCon 2021 | Back To Basics: Undefined Behavior - Ansel Sermersheim & Barbara Geller**

- 介绍了 well-defined, impl-defined, unspecified 和 undefined
- 过了几个 ub 的例子
- 总结就是还是少些“天外飞仙”类的代码，多用用 static analysis tool

**CppCon 2021 | Lightning Talk: One Year of Meeting C++ Online - Jens Weller**

- 和普通开发者关系不大，回顾了一下过去一年 meeting c++ online 这个 program 的情况

**CppCon 2021 | cudaFlow: Modern C++ Programming Model for GPU Task Graph Parallelism**

- 不是我的领域，随便看了一点直接跳过了

**CppCon 2021 | From Problem to Coroutine: Reducing I/O Latency - Cheinan Marks**

- 讲的一个没有到预期的 coroutine 改造的例子
- 总结一下：
- the support of coroutine is not ready yet, wait for c++23 or 26.

    don’t write coroutine lowlevel code yourself, just use a library

    debugger now is unhappy about use of coroutine

    coroutines are not subroutines; a coroutine can run and then resume on a different thread

    the work goes in the coroutine body


**Building Microservices | 4. Microservice Communication Styles**

- blocking 的 request-response, async 的 event-driven, 还有利用 shared store
- 每个 style 都讲了 pros & cons & when to use
- 不过我觉得作者在这章把 async in semantic 和 non-blocking io 混在一起了容易误导

\#2

本周花了点时间看了一下 abseil base/internal/cycleclock 的源码

这个类主要用于 perf counter 类的计时

---

好了这周就这样，下周见
