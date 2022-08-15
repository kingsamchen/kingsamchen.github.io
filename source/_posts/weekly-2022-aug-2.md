---
title: 一周杂记 in Week 2 Aug 2022
categories: CODE-LIFE
date: 2022-08-14 21:18:47
tags: [杂记]
---
本周是 8 月份第二周。

## Life

\#1

这周莫名其妙长湿疹了，腰部加上腹股沟位置整片的斑点，密密麻麻，而且一出汗就很容易发痒。

老婆从科室带了拜耳的艾洛松，擦了几天后有明显好转，斑点开始变成淡红色，但是区域/范围至今都没有明显减小。

估计还要一周的时间来治疗恢复吧。这一周大概都没法运动了，不然一出汗就痒得想抓，很容易抓破皮。

\#2

因为众所周知的原因，上半年的经济形势非常糟糕。以至于好多人都安慰自己说到下半年会好的。

结果7月份的社融数据一出，惨不忍睹。

等消费数据出来，估计只能听见一片哀鸿了。

看着有没有什么强心剂继续打吧。

另外听说北戴河会议快结束了，并没有特别好的结果。难道真的要再继续倒车十年？

\#3

这周看了电影 ice road 和 protege。

这两部感觉都很一般。

Ice road 一开始我以为是灾难片，结果又变成了俗套的剧情片，关键是剧本还很一般...

尼森大叔选片眼光有待加强啊。

Protege 嘛，差不多就是要给无脑动作爽片，关键看起来也不太爽...

Maggie Q 和 Michael Keaton 不来电呢...

不过下周 Better Call Saul 要迎来完结了，非常期待。下半季除了最后一集还没出，其他 episode 都已经把 2160P 下完了，只等完结。

## Work

\#1

上周分析了 Grace 的源码，这周抽个时间看了一下另外一个号称 zero-downtime 的 graceful restart 的 endless 源代码。

整体思路其实和 Grace 差不太多，不过 Endless 直接用 `sync.WaitGroup` 管理 non-closed connections，整体比 grace 依赖的 httpdown 那坨计数和状态迁移要清楚直观很多。

\#2

这周开始刷极客时间的新专栏《透视HTTP协议》

刷了几节，感觉比上一个要诚恳，踏实得多；没有整不相关的诗词歌赋凑篇幅这种幺蛾子。

\#3

CppCon 2020 补完计划方面，刷完了 _C++20 String Formatting Library: An Overview and Use with Custom Types - Marc Gregoire_

这个 talk 的干货我觉得可以直接查 cppreference 来完成。

\#4

这周基本只有周末在看书。

_Software Engineering at Google_ 这种 misc 的看起来会快点，周末差不多看了十几二十页。

_Database Internals_ 越看越难受，现在在考虑要不要弃；但是对于强迫症来说中途放弃实在太难受了

---

本周就是这样，下周见
