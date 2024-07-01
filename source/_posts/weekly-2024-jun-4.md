---
title: 一周杂记 in Week 4 June 2024
categories: CODE-LIFE
date: 2024-07-01 10:20:14
tags: [杂记]
---
本周（06/24 ~ 06/30）是六月份第四周，六月最后一周。

## Life

\#1

周二上午还下着大雨，一个人开车到了省妇保重新做了一次精子检查。

检查结果出来的也比较及时，让媳妇儿看了一下，说：你这年纪精子质量比起年轻人确实差了一些，但是还没到无可救药的地步...

然后就是制定了饮食和生活习惯计划，包括从原来的每天三杯咖啡到隔天一杯咖啡，少吃甜食零食，尽量不喝碳酸饮料这些...然后多补充蛋白质，晚上11点左右上床，保证睡眠质量

这人上了年纪之后确实各种方面都容易出问题 😩

\#2

上周日把 lucky 的骨灰拿回家之后本来打算周五晚上头七的时候到对面的河道公园埋了，但是因为周五下了一天的雨，到了晚上还很大，所以这件事一直拖到了周六晚上。

周六晚还好基本没雨，我拿着JD买的小铲子在河道公园转了一会儿，找了一个感觉不错的地方就开始挖土。

实话说用小铲子挖土确实有点吃力，而且之前这方面没有太多经验，所以之挖了一个不大不小的坑，还好骨灰盒能放进去。

又从边上挖了一些土盖上去，弄弄平，至少看上去不太有问题了

\#3

这周电影俱乐部看了 **唯一的家园** 3/5 内容上还行吧，但是拍摄和叙事太一般了，感觉还不如中国强拆纪录片来的好看…

\#4

电动车电瓶又挂了...

骑到 8% 的时候突然自己掉电，推回家后发现充电也充不上...

只能等到雨停了找个天晴的时候推到店里检修一下。上一次电瓶挂了的时候给我免费换了一个，这次不知道还能不能免费换一个

## Work

\#1

**Lazy Futures with Coroutines** https://www.modernescpp.com/index.php/lazy-futures-with-coroutines-in-c-20/

- 这个 Post 把之前 eager coroutine 改成了 lazy coroutine，本质是通过调整 promise_type 的 initial_suspend 和 final_suspend 返回的 awaitable 来做到的
- 前后对比可以对 coroutine execution workflow 有一个更好的认识

**Executing a Future in a Separate Thread with Coroutines** https://www.modernescpp.com/index.php/executing-a-future-in-a-separate-thread-with-coroutines/

- 在前篇的基础上讲如何将 coroutine 放到其他线程去 resume/execute
- 容易上手

**An Infinite Data Stream with Coroutines in C++20** https://www.modernescpp.com/index.php/an-infinite-data-stream-with-coroutines-in-c-20/

- 开始讲 co_yield 以及如何实现简单的 generator
- 另外有个优点是把 `initial_suspend` 和 `yield_value` 返回的类型分别是 `suspend_always` 和 `suspend_never` 会发生什么事分析了一下，感觉很不错

**A Generic Data Stream with Coroutines in C++20** https://www.modernescpp.com/index.php/a-generic-data-stream-with-coroutines-in-c-20/

- 这篇在上篇的基础上引出 awaitable 三大函数并且用伪码描述了一下 workflow

**Starting Jobs with Coroutines** https://www.modernescpp.com/index.php/starting-jobs-with-coroutines/

- 实现一个 job coroutine wrapper 并且用自己实现的带注释的 suspend always 和 suspend never 来探究 awaitable 的 workflow

**Automatically Resuming a Job with Coroutines on a Separate Thread** https://www.modernescpp.com/index.php/automatically-resuming-a-job-with-coroutines/

- 系列最后一篇，再其他线程 resume coroutine
- 这里 task 的 initial_suspend 和 final_suspend 都变成了 suspend never，不知道是不是为了简单起见

**Move Annoyance** https://sean-parent.stlab.cc/2021/03/31/move-annoyance.html

- 如果一个类内部用(智能)指针管理了某个资源，那么 moved-from 状态之后，这个指针可能会被实现成 nullptr，这虽然是符合标准对于 moved-from object 要能够被正确析构的要求，但是如果 copy assignment 或者 move assignment 实现的不太对，就有可能出现对这个 nullptr 指针的解引用
- 并且要非常注意避免因为这种内部实现要引入一个 empty state 来处理 equality 语义

**CppCon 2021 | Using Clang LibASTMatchers for Compliance in Codebases - Jonah Jolley**

- 利用 libclang 来做 code compliance 的 search/match
- 思路不错但是有点曲高和寡

**CppCon 2021 | Finding Bugs Using Path-Sensitive Static Analysis - Gabor Horvath**

- 作者是研究静态分析的 PhD 现在在 MSVC team 做静态分析，就是 MSVC static analyzer 那个
- 但是讲的算法门槛太高了哥，完全听不懂，只能大概 get 到基础的利用几个约束做线性求值看是否存在可能性的部分

**CppCon 2021 | Zen and the Art of Code Lifecycle Maintenance - Phil Nash**

- quality system
- handling error
- use type to restrict input domain

**C++ Weekly - Ep 265 - C++20's std::bit_cast**

- type punning 在 C++ 中是 UB，所以也是合法的
- 另外，不管是 reinterpret_cast 还是 `std::memcpy` 都没法在 constexpr 中使用，而 `std::bit_cast` 是允许的

\#2

最近写 learn-cxx-coroutine 的时候发现生成的 main module 的 CMakeLists.txt 中的 sanitizer 的设置遗留了之前 POSIX only 的部分没有清理，所以就抽了个时间 cleanup 了一下

https://github.com/kingsamchen/anvil/pull/18

\#3

最近学习 moderncpp 的 learn coroutine mini-series 照葫芦画瓢写了一些代码，但是都是学习性质的就不放了。

另外上周基本没有抽时间看书和做线性代数的习题 🤣

---

好了这周就这样，下周见
