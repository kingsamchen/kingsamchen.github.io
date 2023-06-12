---
title: 一周杂记 in Week 2 Jun 2023
categories: CODE-LIFE
date: 2023-06-13 00:49:35
tags: [杂记]
---
本周（06/05 ~ 06/11）是六月份第二周。

## Life

\#1

这周看了两部电影

- 乱 5/5
  大师黑泽明指导的日本版李尔王。虽然是旧故事，但是演绎的是真好
- 盟约 3.5/5
  周三电影俱乐部和同事们一起看的。没想到盖里奇一个英国人跑去拍美国主旋律。不过严格来说这篇其实是在讽刺美国政府背信弃义

\#2

最近经济下滑的势头太明显了，大家还是勒紧裤腰带，小心谨慎过日子吧。 🤦‍♂️

## Work

\#1

本周学习进度

**雪球基金第一课 | 2. 看重稳健理财，选固收基金**

这章讲的非常不错，对债券基金，固收+基金都介绍的到位；同时还分享了一些分析基金的常见思路

**CppCon 2021 | C++20: Reaching for the Aims of C++ - Bjarne Stroustrup**

这个可以看作之前 C++ 白皮书的 BS 真人现场版

**CppCon 2021 | Lightning Talk: That's a @meta rotate! - Kris Jusiak**

核心是 metaclass 可能带来的一些元编程改变

**DDIA | 5. Replication**

这周看了得有十几页吧，不过没看完，还剩下一些，下周预计能看完。

这章讲了常见的几种 replication 机制，真是大开眼界。

\#2

这周花了点时间，给 anvil 做了一个比较大的调整。

首先是用 jinja2 作为文件模板，好处是对模板内容做变更更加直观，并且 jinja2 也支持条件语法，搭配变量注入之后再也不用原来核心脚本中辛苦的判断上下文论及拼凑生成文本了。

另外就是同步更新了 linux 上 compiler warning flags 和 clang-tidy checks。

整个调整可以看 MR https://github.com/kingsamchen/anvil/pull/5

---

这周就这样，下周见
