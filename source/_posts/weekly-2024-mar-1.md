---
title: 一周杂记 in Week 1 Mar 2024
categories: CODE-LIFE
date: 2024-03-11 20:49:30
tags: [杂记]
---
本周（03/04 ~ 03/10）正式进入三月份的第一周。

## Life

\#1

这周社交活动比较多，周二刘千万找我吃黄牛肉火锅，直接把和同事的健身翘了；周四 Ricardo 请吃三分银直接辣的晚上肠胃快扛不住...

周六和俩发小看完电影后也吃了一顿

周六天气非常好，媳妇儿说中午想出门吃饭然后转转，我觉得靠谱。

但是没想到她听朋友推荐的那家网红店“徐春娇下饭菜”让我一口就觉得是预制菜...

于是吃到后面媳妇儿也很生气；吃完后跑到最近的一家商场点了一杯霸王茶姬才勉强压住媳妇儿的怒火。

而本来打算坐公交或者走走到家，结果一查地图公交站都要走快两公里，而且直接走到家也就3公里多一点，想了想最后还是打车回家了 🤣

不过还好天气确实不错，心情愉快一些

\#2

这周组里的“前途”经历了一次过山车。

细节就不讲了，只能说前一天大家还在想着可能都抗不过一个月组就没了，结果第二天就变成还能再续命一年 🤣

我们组作为中央政治局体验服，大家真的是快给各种奇葩现状弄蒙蔽了

\#3

本周观影：

- **沙丘2 Dune Part Two** 5/5 吹爆，part two 如此高密度的信息量下也只有前半段出现一两次的节奏跳跃，Jesus的宗教化用毫无违和感，完成度拉满。打算后面和同事约个二刷
- **雷霆沙赞！众神之怒 Shazam! Fury of the Gods** 2/5 离谱程度堪比金刚狼2，Ezra Miller 和闪电侠千古奇冤

这周因为有点累，所以周三电影俱乐部的 Poor Things 没有去看。

## Work

\#1

本周学习

**Design Patterns in Modern C++ | 8. Composite**

- 这张看完我都没 get 到他想表达的 composite pattern 到底是为了解决什么问题…
- 这章的水准确实太挫了

**CppCon 2021 | Embracing User Defined Literals Safely for Types that Behave as though Built-in - Pablo Halpern**

- 介绍如何自己定义的 UDL 以及目前常用的 UDL有三种
    - cooked udl
    - raw udl
    - template udl

**Everything You Know About Latency Is Wrong** https://bravenewgeek.com/everything-you-know-about-latency-is-wrong/

- latency 不服从正态、高斯、泊松等分布，所以他们的均值、中位数甚至标准差都是没有用处的
- 导致延迟的可能原因有非常多并且出现的非常随机，可能是 gc pause，hypervisor pause，上下文切换甚至发生磁盘写入操作
- 有意义的延迟度量应该看半分位 percentile，但是 average percentile 这个维度也是没有任何意义的
- 延迟的最大值不能忽略，他不是 noise，而应当被当作 signal 考虑；只有当你确认了导致最大延迟的原因是类似 vm 重启这种之后才能确认他是 noise
- 测量中的 coordindated omission: What coordinated omission is really showing you is *service time*, not response time；HdrHistogram 是个好工具

**CAP and the Illusion of Choice** https://bravenewgeek.com/cap-and-the-illusion-of-choice/

- 这篇文章有点老了，所以他的观点很多人应该都知道了，那就是虽然是CAP但是P是几乎不可选的，所以只有AP和CP
- 用原文的总结来说就是：“*You have AP, CP, or None of the Above. The problem with None of the Above is that it’s difficult to reason about and even more difficult to define.*”

相对比较少，因为一直忙着做下面这个东西，消耗了太多的精力

\#2

我们组的项目用的 nlohmann-json 但是因为这个东西没有提供特别好用序列化宏，原生（库提供）的宏局限性特别大，甚至都不能指定 key name，这还玩毛，所以之前大家一直都是人肉处理具体的 json fields，就非常的难受。

然后我们现在 security policy 对 Github 上来路不明的 3rd party 卡的比较严，而且我看重的一个带静态反射的库 glaze 需要 C++20 支持；估计到我跑路这俩条件都没有解决的可能性。

所以想着要不自己搓一套宏，直接基于 nlohmann-json 利用宏把 from_json/to_json 给自动补上得了...

这周算是比较正式开始做这个事情。

然后因为大量基于宏，所以局限性比较大，有一些想法实现起来特别绕，所以花了大量时间在上面。

经过几天高强度尝试和修改，目前有一个雏形版本，所以希望是下周能完成。

\#3

revisit 深度学习 chapter 5 缓慢继续中...

---

好了这周就这样，下周见
