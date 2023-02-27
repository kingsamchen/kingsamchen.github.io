---
title: 一周杂记 in Week 4 Feb 2023
categories: CODE-LIFE
date: 2023-02-27 23:26:11
tags: [杂记]
---
本周是二月份最后一周，下周就要进入三月份了。

## Life

\#1

过去一周感觉右腿一直莫名的酸痛，甚至有点绞痛，但是又说不上是哪里痛。

媳妇儿一顿检查说看起来不像是肌肉的问题，没准是关节炎了。让我好好休息先停几天跑步，去健身房就练练力量就行。

总担心是年纪大了小毛小病会很久才能痊愈。

\#2

这周看了两部电影：

- 神秘海域 6/10 堪堪及格的爆米花吧...
- 画圣 5/10 老婆选的，画面构图很好，但是故事讲得稀烂，而且节奏也很差。白瞎了午马老师的表演。
  事后一查发现导演确实是摄影系出身的...

## Work

\#1

本周学习进度：

《Apache Kafka 实战 | 5. Consumer 开发》 和 _Database Internals | 14. Consensus_ 都差不多看了一半，并且下周这两章基本都能看完。

_Database Internals_ 终于快完结了...这书看的我快要胃疼...

CppCon 2020

- _A Physical Units Library For the Next C++ - Mateusz Pusz_ 讲的一个物理单位的库，做科学计算的估计需要。这库的竞争对手是 boost.unit
- _Modern Software Needs Embedde`d Modern C++ Programming - Michael Wong_ & _Dealing with Embedded Limitations - Panel Discussion hosted by Ben Saks_ 这俩讲的都是嵌入式应用场景的问题。和我自己的场景相差比较大

\#2

继续写了一部分 esld/tld 算法的实现，进度也就那样吧，感觉细枝末节还是坑不少

\#3

接了一个 task，需要用 Java Spring JPA 来操作 MySQL。

原本简单的事情给 JPA 搞得繁杂的一笔，尤其 DAO 对象中 `DeleteById()` 这个自动生成的操作居然会首先 `SELECT` 然后再去 `DELETE`，这就导致 delete 一个不存在的 entry 会直接抛异常。

而且更奇怪的是文档说这种情况会被 silently ignored...害得我惨遭 US 同事打脸。

Java Spring，专注让简单的事情复杂化...

---

好了这周就这样，下周见
