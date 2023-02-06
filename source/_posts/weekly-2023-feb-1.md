---
title: 一周杂记 in Week 1 Feb 2023
categories: CODE-LIFE
date: 2023-02-06 22:01:08
tags: [杂记]
---

本周年后开工第一周

## Life

\#1

这周开始上班了，但是感觉状态切换到上班装还需要一些时间。

其中有一部分因素是因为私下开始传的年后可能会有 layoff；另外一部分原因是福利和bonus砍的太狠了以至于大家没有太大的激励动力了。

\#2

老婆的奶奶突然情况开始恶化，感觉有点要病危了...不知道能扛多久

这几天老婆情绪不是很好，之前她觉得病情能控制住并且以她的经验来看有很大概率可以康复；但是县医院的医生水平感觉还是捉急了...

\#3

这周抽了时间看了 2001 太空漫游。

1960 年代的视角来看这电影确实有点牛逼，概念和想法都是远远领先。但是60年后再来看，很多手法其实都已经融入到其他电影中了。

所以怎么说呢，大人时代变了。

## Work

\#1

之前修 esl 的 clang lint 的时候突然想到写的 unique_handle 如果值为空会不会还去调用 `Traits::close()`

如果是的话会有两个问题：

- 依赖 underlying deleter 能够正确处理资源为空的情况，不然可能就是个UB
- 多一次调用会影响 `errno` 或者 `LastError`

还好翻了一下 cppreference 发现应该是有标准要求 unique_ptr 只有在 not nullptr 的时候才会调用 deleter。

顺带加了一个 test case 来验证这种情况。

\#2

本周学习进度

- _Software Engineering at Google_ 看完了 _24. Continuous Delivery_
  CI/CD 才是 delivery speed 保证啊；不过其他人倒是没法直接借鉴；比如我们的 code base...
  另外这章是倒数第二章了
- _Database Internals_ 看完了 _11. Replication and Consistency_
  这张最大的收获就是让我有点点理解了 linearizability...
- Apache Kafka 实战 看完了 3. Kafka 线上环境部署

另外还有同步的 Kafka 实战的极客时间课程。夸一句这课真不错..

CppCon 看了俩

- The Surprising Costs of void() (and Other Not-Quite-Innocuous Evils) - Patrice Roy
  这个 talk 还值得一看的，内容不多，针对的是实现中的一些小“习惯”问题
- Collaborative C++ Development with Visual Studio Code - Julia Reid
  VS Code 的功能广告

---

好了本周就这样，下周见
