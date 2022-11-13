---
title: 一周杂记 in Week 2 Nov 2022
categories: CODE-LIFE
date: 2022-11-13 23:17:49
tags: [杂记]
---
本周是 11 月的第二周。

## Life

\#1

这周产品终于 GA + public beta 了，于是接连几天都盯着产线和我们 staging 的情况，同时化身客服和 Q&A 解决各种答疑。

因为老婆周三从富阳回来，所以选择周三 WFH，吃了一顿不错的大闸蟹参补了补状态。

但是感觉还是好疲乏，所以周五专门休了一天年假，在家里划划水看看电视和电影。毕竟老婆周六又要跑去富阳轮最后一次勤。

\#2

这周跑了4次5KM，算上上周六的那次，最近8天是跑了25KM；尤其周五，六，日三天连续5KM...

感觉一周20KM还是有点难度额，主要周末两天接着上下周基本无休，5天工作日还要抽两天下班后跑。

有时候和老婆选一两天在小区打乒乓球的话就基本压缩了跑步时间，如果一周7天都在大强度运动的话感觉对身体也是一种伤害...

下周回头再看看如何安排劳逸，免得越运动死的越早。

\#3

本周和老婆看了大河恋(A River Runs Through it)，我个人是非常喜欢这种风格的片子，虽然节奏慢，但是丝毫不感觉拖沓。以一种回忆式的展开娓娓道来。

不过老婆觉得很没意思，被我连续说了几句不懂得欣赏。我能给到 8.5/10

另外看了 1/3 的 Event Horizon，不过自然是删减版的，没有 Rick 都看吐的片段...

（我在 youtube 上找了删减片段，是真的很冲击心灵...）

## Work

\#1

本周学习进度：

- 看完了 _Software Engineering at Google | 14. Larger Testing_ 和 _15. Deprecation_
  我们系统里的 end-to-end tests 本质上其实就是 large tests 了，书里提到的一些问题确实也遇到了。不过再大规模的目前还没有一个认知。
  Deprecation 对下限旧系统还是有指导意义的，不过我们团队目前规模还处于可控，并且人员素质还都比较高，没有在这方面没有出现太大的冲突
- 看完了《大规模分布式存储系统 | Chapter 8 OceanBase 架构初探》
  我就感觉到不明觉厉
- 刷完了《网络编程实战》
  有些许干活，但是整体仍然是面向新手；而且后面的例子其实随意了一点。
- 看了 _CppCon 2020 | Managarm: A Fully Asynchronous OS Based on Modern C++ - Alexander van der Grinten_
  涨姿势系列

\#2

这周看了三个不同的 bloom filter 实现，分别是：

1. github 上的野生实现 https://github.com/fylux/BloomFilter
2. Redis Bloom 用的带优化实现 https://github.com/RedisBloom/RedisBloom/tree/master/deps/bloom
3. Chromium 用的朴素实现 https://source.chromium.org/chromium/chromium/src/+/main:components/optimization_guide/core/bloom_filter.h

顺带记了一点代码分析过程中的笔记，回头有时间的话单独开个 post 记录一下

\#3

这周本来打算看完别家实现之后自己也亲自实现一个，不过看起来时间有限（主要我太能浪费时间了），所以估计最多只能开个头，下周才能正式开始。

---

好了本周就是这样，下周见
