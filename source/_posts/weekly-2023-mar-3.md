---
title: 一周杂记 in Week 3 Mar 2023
categories: CODE-LIFE
date: 2023-03-20 19:29:08
tags: [杂记]
---
本周（03/13 ~ 03/19）是三月份的第三周。

## Life

\#1

发小原定 2021年12月的婚礼 延期了一年多之后最终确定在本周六举行，我也是被他要求当伴郎，虽然我也不懂我一个已婚的怎么可以给人家当伴郎，但是发小说了：组织已经决定了，你要来当伴郎。

讲道理当伴郎是真累，尤其是这种冗长的婚礼流程里。

一大早（6：30）就到新郎家换了衣服，还好只需要多穿个马甲，白衬衫和黑裤子可以用自己的，然后开始各种拍照。

到点之后去新娘家又一波游戏接到新娘然后接回新郎家又一波活动然后去了酒店。

到了酒店之后又一波拍照，然后开始婚礼流程。

流程差不多之后终于可以坐下吃饭了...

讲道理我实在不能理解为什么大家会这么热衷这种婚礼仪式，我自己当初结婚都没搞仪式，可能这得感谢我媳妇儿？

\#2

这周看了三部电影

- The Whale 8/10 今年奥斯卡最佳男主的电影。我个人感觉话剧感有点重，但是男主表演确实没话说
- 和媳妇儿一起重新回顾了 The Fall 7/10。我个人还是比较喜欢这个电影的，一些评价可以看之前的日志
- The Social Dilemma 8/10 又名搜广推如何影响人的意识和决策。作为一个程序员，我非常推荐这部纪录片

## Work

\#1

这周研究了一下 golang 某个配置库 koanf，原因是贵司的容器化部署做的太烂了，目前都不支持配置中心，不同环境的配置只能要求配置系统能够做到 override 同名配置项来控制...

C++ 服务因为有 gflags 这种奇葩的库，所以大家觉得都还好，除了 devops 每次部署都想死...

但是 golang 原生可没法这样做，于是调查了一圈，又问了 Vizze 他推荐了 viper。

研究了一下 viper 觉得太复杂了，本来都想放弃了没想到发现有个 lightweight viper 的 koanf；看了几个 examples 之后觉得 koanf 可用，于是围绕 koanf 做了一个 wrapper，试了一下也基本做到了预期。

\#2

本周学习进度如下

(1) 看完了两篇 coordinated omission 相关的 posts。

不得不说这个问题真是冷门，以至于 new bing chat 都没能给我啥有效信息

(2) DDIA 看完了 _1. Reliable Scalable and Maintainable Applications_

阅读起来非常流畅，这一章其实也提到了 coordinated omission，但是是以 latency 和 response time 作为对比。

(3) Apache Kafka 实战 继续此前的 6. Kafka 设计原理，差不多看了20来页

另外对应的极客时间课程也在继续刷

(4) CppCon 2020 | Dynamic Polymorphism with Metaclasses and Code Injection - Sy Brand

这个 Talk 有点意思，但是不是很体系/系统。

Talk 里首先引入另一种形式的不需要继承的运行时多态的实现，然后简化 boiler plate 引出 static reflection 和 metaclass code injection

整体上属于 what can we do if we had these features in set.

---

好了本周就是这样，下周见
