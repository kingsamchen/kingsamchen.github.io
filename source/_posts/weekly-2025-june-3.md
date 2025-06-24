---
title: 一周杂记 in Week 3 June 2025
categories: CODE-LIFE
date: 2025-06-23 21:42:33
tags: [杂记]
---
本周（06/17 ~ 06/22）是六月份的第三周，这周大事一件接一件。

## Life

\#1

虽然周日 22 号才是秋羽宝宝的满月日，但是考虑到周日要搬东西离开月子中心，所以月子中心那边提前一天给秋羽搞了满月 party。

娃全程都比较稳定，非常配合的踩了脚印，拍了各种照片，不哭也没闹，感觉真的是天使型宝宝。

除了娃脾气像我，非常差。有什么需求发出信号半分钟内没有响应，就会用女高音的高分贝持续广播；这个时候只要立马满足她的需求，直接瞬间变乖巧脸。

因为最近开始胀气，然后能听见明显的屁声，媳妇儿说秋羽放屁的频率和声音不输我，加上小石榴的小名，现在我们给她加了一个称号：屎（石）总 😊

因为周日要离开月子中心，所以周六提前搬了一部分东西到媳妇儿家。

周日我开车提前到，顺带研究了一下宝宝安全座椅的使用。待到媳妇儿和丈母娘把屎总安抚好之后便把剩余的东西和屎总一起弄上了车。

看屎总反应这安全座椅非常舒适，路上屎总一点都没醒，睡的都打起了小呼噜 😂

到媳妇儿家之后花了点时间一起把东西整理了下，屎总也是无缝衔接，适应得非常快。

家里的大床比起月子中心的移动小床那舒服了不知道多少倍，嘿嘿

\#2

这周二开始上班，然后发现老张狗日的不合我代码，一直找借口说还没 review 完。

本来想着不知道这次这 PIP 咋搞，大K总该咋保我。结果周四上午还没等到 daily sync，就听到小道消息说老张美国时间下午去找 CDO 要 HC 的时候被通知下课了，直接降级为 IC。

老张直接傻逼，说要找 Eric 申诉，然后就失联了，周四周五都没参加 daily sync。

CDO 找双C开了会传达了核心点；杭总说美国那边有个别人也已经被 CDO 开会通知了，所以基本就是这样了。

所以我这 PIP 还没开始就结束了，真的笑死。难怪K总和S总这么淡定，原来早就已经运筹帷幄就等最终一击了。

据说这次闪电处理的直接导火索是 Lionel 的离职，所以现在看起来真的就是蝴蝶效应了。当时老张对 Lionel 跑路丝毫不在意，大概觉得杭州外包走了就走了呗。

按照老张的性格，估计再混一段时间就跑了，毕竟从 head 降级到 IC 真够他丢人的。

周五团建的时候大家非常放松，一边吃饭一边闲聊，一边嘲笑老张。

对，周五终于团建了，算上上次的经费，这次直接人均600。选的灵隐内的一家米其林，浙菜，味道还行，就是感觉这人均有点不太值得，而且交通是真的有点烂。

另外就是我们新 mananger 的领导风格和未来灾后重建目前还没有定数。因为后面也不是K总负责我们了 🤔

不过不管怎么说，应该不会比在老张手下更差了。

\#3

这周太忙了（娃 + 上班要应付PIP刷代码行数 + 下班还要刷题）没有看电影。

下周看看有没有空闲。

## Work

\#1

**CppCon 2022 | Understanding C++ Coroutines by Example: Generators (Part 2 of 2) - Pavel Novikov** https://www.youtube.com/watch?v=lz3F036_OvU&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=30

- 以 generator 为例讲解 coroutine 下篇

**C++ Weekly - Ep 485 - Variadic Structured Bindings in C++26** https://www.youtube.com/watch?v=qIDFyhtUMnQ

- C++ 26 支持了 `const auto &[...members] = obj` 这样的处理
- 如果记得没错的话有一些 compile-time ser/de 就是用的类似的 trick，有了这个的话一些 ad-hoc ser/de 感觉都可以手写了 😂

**C++ Weekly - Ep 484 - Infinite Loops, UB, and C++23** https://www.youtube.com/watch?v=Zkm1ZaNn3ko

- 核心点是 C++ 26 开始 infinite loops 不再是 undefined behavior 所以以前的一些利用 infinite loops 构造的 ub trick 可以退场了

**C++ Weekly - Ep 328 - Recursive Lambdas in C++23** https://www.youtube.com/watch?v=hwD06FNXndI

- lambda + deducing this = recursive lambda

**C++ Weekly - Ep 330 - Faster Builds with `extern template` (And How It Relates to LTO)** https://www.youtube.com/watch?v=pyiKhRmvMF4

- extern template 因为提前准备了实现代码，所以利用 LTO 可以更好的做到跨 TU 优化

**C++ Weekly - Ep 329 - How LTO Easily Makes Your Program Faster** https://www.youtube.com/watch?v=9nzT1AFprYM

- 展示 LTO 的作用

**[C++] Static, Dynamic Polymorphism, CRTP and C++20’s Concepts** https://www.codingwiththomas.com/blog/c-static-dynamic-polymorphism-crtp-and-c20s-concepts

- 基本属于蜻蜓点水，没有深入的展开，权当作 overview 复习吧

\#2

这周因为开始的时候不确定会不会被老张 PIP 干走，所以不仅这周学习的时间少，而且时隔4年又打开了 leetcode 开始准备刷题。

结果刚刷了四道链表题（这四道还居然都做出来了，虽然明显比起一起花了很长时间）老张就下课了。

这样的话暂时就不用刷题了，oh yeah

因为后面 reorg 之后就是灾后重建 remake 了，所以 asio 相关的又可以看起来了

---

好了这周就这样，下周见
