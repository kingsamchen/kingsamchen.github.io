---
title: 一周杂记 in Week 5 Oct 2022
categories: CODE-LIFE
date: 2022-11-04 00:45:49
tags: [杂记]
---
这篇算是迟到的更新了，所以内容会简明扼要一些...

以下是十月第五周也即最后一周的杂记

## Life

\#1

做了两年多的项目终于马上要 GA 了，最近的工作节奏也开始变得快起来。

Client team 那边因为一些不可抗力因素更惨，需要临时加班。而且有的同学已经连续奋战两周多了。

虽然周末加班有 2x 工资，但是这种不按计划的变动始终是大家忧虑的一个主要来源。

11月前半个月估计要忙成狗了。正式宣布的时间是 11.08 前一周和后一周都会在紧张的节奏中度过

\#2

本周看了两部电影：

- 《万湖会议》https://movie.douban.com/subject/35769174/
- 重看了《源代码》

万湖会议是一个很神的影片，场景少，完全依靠对白和人物的刻画来呈现。

最搞笑的是看完电影之后同事群有个同事发了几条和防疫集中营有关的消息...只能说非常应景了

《源代码》是陪老婆看的，虽然大一还是大二那会儿看过，但是现在看来还是非常的优秀

这两部电影我都能给到 8/10

## Work

\#1

周末终于抽出时间给项目用的 smtp client 提了一个 SMTPUTF8 支持不对的 issue，见 https://github.com/somnisoft/smtp-client/issues/15

其实我也有一个 fix patche，不过先看看作者咋说先。

而且看更新频率似乎都已经弃疗了

\#2

这周学习进度如下：

- CppCon 2020 | Just-in-Time Compilation: The Next Big Thing? - Ben Deane & Kris Jusiak
  这个 talk 有点意思，介绍了 clang 支持的一个实验性的 attribute，可以讲一段代码标记为 JIT 模式。
  搭配 C++ 强大的模板元编程可以做出一些很有意思的东西
- CppCon 2020 | Macro-Free Testing With C++20 - Kris Jusiak
  作者推销自己写的一个 ut framework，μt，目前被收录在 boost-ext 里。不过我对这个 lib 不是很感冒，主要是 macro-free （对于 unit testing framework）在我来看不是一个很有吸引力的点。
- CppCon 2020 | Making Games Start Fast: A Story About Concurrency - Mathieu Ropert
  这个 talk 值得推荐，作者从自己实际项目遇到的情况出发，介绍了如何利用并发来优化游戏加载的时间
- 看完了 大规模分布式存储系统 | Chapter 7 分布式数据库
  这章其实只有三块：1) 传统 mysql 的 sharding 实践 2) azure cloud sql sever 3) spanner
  其实就是传统实践到分布式 RDMBS 的一个路子
- 继续再刷 《网络编程实战》每天花个几分钟，感觉还凑合 🤣

---

好了本周就这样，下周见