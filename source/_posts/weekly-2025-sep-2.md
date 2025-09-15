---
title: 一周杂记 in Week 2 Sep 2025
categories: CODE-LIFE
date: 2025-09-15 23:44:57
tags: [杂记]
---
本周（09/08 ~ 09/14）是9月份第二周。

本周依然事情特别多

## Life

\#1

这周周末依然常规带娃。

娃三个月多20来天已经能正常翻身了，而且也特别喜欢练习翻身。

就是翻身趴着的时候容易流口水，口水巾一直要换。

\#2

这周前同事马总突然说他被滴滴裁了...而且可以确认和绩效没有关系

哎，天朝这个就业环境现在是真的不太行

约了他下周一起吃个饭，不然估计他立马要回菏泽老家躺一段时间了

\#3

本周观影

- **天国与地狱 Highest 2 Lowest** 3/5 除了最后两幕之外其他部分都太强行刻意了….
- **警戒结束 End of Watch** 4/5 洛圣都基层警察记实

## Work

\#1

**CppCon 2023 | Delivering Safe C++ - Bjarne Stroustrup** https://www.youtube.com/watch?v=I8UvQKvOSSw&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=2

- 老爷子讲了很多，不过总结下来还是静态分析工具+guidelines+profile 这套方案
- legacy code 是债务也是资产

**CppNow 2024 Lightning Talks | Debugging Like House - Software Differential Diagnosis - Michael Okyen** https://www.youtube.com/watch?v=qwtVc5OATkE&list=PL_AKIMJc4roVqkYxO--bh9Dn-wym8zGsY&index=5

- 学习豪斯医生的 troubleshooting 思路
- 列表，然后验证/排除

**CppNow 2024 Lightning Talks | Reflecting Types in C++17/20 - Patrick Roberts** https://www.youtube.com/watch?v=whc-iGcVQYA&list=PL_AKIMJc4roVqkYxO--bh9Dn-wym8zGsY&index=4

- 用一些 trick 实现 compile-time reflection
- 作者自己有个简短的 PoC 以及 Kruss 有个 github.com/boost-ext/mp

**CppNow 2024 Lightning Talks | Hilbert Hotel - The Grand Reopening - Tobias Loew** https://www.youtube.com/watch?v=M8rWPS-eLmU&list=PL_AKIMJc4roVqkYxO--bh9Dn-wym8zGsY&index=3

- 一起来上高数课？

**C++ Weekly - Ep 287 - Understanding `auto`** https://www.youtube.com/watch?v=tn69TCMdYbQ

- 复习一下，尤其 `auto` wil never deduce reference 这点要牢记
- const-ness 对于 reference/pointer 是会自动推导的

**C++ Weekly - Ep 288 - Quick Perf Tip: Prefer** `auto` https://www.youtube.com/watch?v=PJ-byW33-Hs

- auto deduction 不会触发 implicit conversion 所以很多时候有性能优势
- 里面提到的遍历一个 map 的时候忘了 key 是 const T 会导致创建一个 temporary 是之前很常见的坑

**C++ Weekly - Ep 289 - Returning From The `void`** https://www.youtube.com/watch?v=26PtAmYk12M

- 核心就是即使 void fn() 这样一个函数 return fn(); 也是合法的
- 评论区有掉这个坑经历的

**C++ Weekly - Ep 290 - C++14's Digit Separators and Binary Literals** https://www.youtube.com/watch?v=Yop9D3V2KBk

- 60%的内容是广告…
- 核心就是 `'` 这个 digital separator

**C++ Weekly - Ep 496 - Stack vs Heap** https://www.youtube.com/watch?v=VAzfOxLNLl4

- 纯科普，没啥内容…

\#2

这周公司的一个全员大改日志已符合合规性的大事项正式开始了，目前预定的 deadline 是9/30，但是看样子不一定赶得上。

US那边的人太不给力了，加上老崔这边还想着并行搞其他的

反正不能太有责任心，不然心态真的会失衡

反正就这么着吧

\#3

这周闲暇时间在踩 url 和 query string 的坑，做到一半发现 boost::url 处理 invalid query string 时候会整个 parse 报错，所以要对 query string 部分保持一定的容忍度，还需要自己提前做 split。

另外观察了一下 gin 对于 invalid url 部分的处理，例如 url 编码后出现不合法的 `%FG` 这种，gin 是直接框架层触发 400 Bad Request 然后 close connection，感觉这部分可以借鉴一下。

然后这又引申到另外一个点，就是 serve request 的时候怎么处理 transport layer 的错误和应用层主动期望的错误（400 或者 401 这种）

这个之前没那么快想到要立马处理这个 🤔

---

好了这周就这样，下周见
