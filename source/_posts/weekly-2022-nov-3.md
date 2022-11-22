---
title: 一周杂记 in Week 3 Nov 2022
categories: CODE-LIFE
date: 2022-11-21 23:31:43
tags: [杂记]
---
本周是 11 月的第三周。

## Life

\#1

这周把 _Event Horizon_ 看完了，影片本体是删减了惊爆部分的，但是在 youtube 上看完了相关惊爆的解说...

这片子怎么说呢，算是一个很到位的 cult 片了吧...不过感觉还是形式重了一点，整体 3.5/5

_Nightmare Alley_ 开了一个头，大约 1/3，下周可以抽点时间看完。

好像现在这种断断续续的看片对我也不会有太大的影响...

\#2

这周买的 Herman Miller Aeron 终于到货了。

整体体验还是很赞的，确实是比以前的椅子都舒服，不过考虑到超高价格，性价比到没到就不知道了。

但是整体的满意度还是可以的，毕竟趁着钱还在自己手里的时候该买啥就买啥。

Aeron 整体感觉最明显的就是背部支撑做的非常到位，而且可微调的地方特别多，对我这样的强迫症来说是个福音。

![](img/FhxqSqNUAAAcgkZ.jfif)

![](img/FhxqSqqVQAEcRBg.jfif)

\#3

这周四组里为了庆祝产品正式发布做了一次小团建。首先去了御牛道吃烤肉，但是因为人均经费此次只有百来块每人，所以基本上吃不了多少肉。

下午在同事家里玩了一把 Switch 的 Mario Party，之后接盘另外一个要回家的同事的德扑局。

因为之前已经注资50%给另外一个场上同事，所以算是上了杠杆；最终下来赢了80来块钱，算是有点小赚。

\#4

周五一个前同事来杭州，晚上一起吃了牛肉火锅。味道感觉还行，量也凑合

## Work

\#1

这周终于吧 bloom filter 的代码写完了，见 https://github.com/kingsamchen/Eureka/tree/master/bloom-filter/bloom-filter

整体上学习了 redis-bloom 的方式，一些实现 trick 也借鉴了不少 XD

下周可以开始准备 cuckoo filter 了。

\#2

本周的学习进度：

- 看了 CppCon 2020 | Neighborhoods Banding Together: Reasoning Globally about Programs - Lisa Lippincott
  但是实话说我完全不知道这个 talk 讲的啥，并且我一直觉得这 talk 能上是因为 presenter 是个女的
- 大规模分布式存储系统 | Chapter 9 分布式存储引擎 看了第一部份
  第一部分是 oceanbase 的一些基础设计
- 两篇 posts https://ferd.ca/operable-software.html 和 https://rachelbythebay.com/w/2019/01/20/quiet/
  前者讲为什么要 observability 已经如何应该给系统加上 obsevability，不过我感觉这篇口水有点多
  后者讲了一个小故事，试图说明一些事后看起来很简单追查的故障其实是很难追查分析的，那些网络上侃侃而谈的键盘侠没有自以为的牛逼

\#3

这周学到了一个 GCC/Clang 的编译告警控制技巧：如果开了 `-Werror` 但是有希望某些 warning 不要升级为 error，一般都有对应的 `-Wno-error=xxx` 来 switch off

比如可以用 `-Wno-error=deprecation-declaration` 来允许只告警使用了 `[[deprecation]]` 的东西。

---

好了本周就是这样，下周见
