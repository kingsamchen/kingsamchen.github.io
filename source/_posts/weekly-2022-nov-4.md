---
title: 一周杂记 in Week 4 Nov 2022
categories: CODE-LIFE
date: 2022-11-27 23:44:56
tags: [杂记]
---
这周是11月第四周，也是最后一周。

## Life

\#1

这周世界杯开幕了，也看了几场比赛，冷门的也不少。

小组赛估计还要持续到下周才会进入淘汰赛。

想了一下好像没有特别支持的球队，纯粹当作欣赏运动了；唯一的希望就是我厂球员不要受伤，英超下半程继续给力

\#2

本周看了三部电影

- _Nightmare Alley_ | 剧本比较和我胃口，并且我很喜欢 Bradley Cooper 在此片里的表演。 7.5/10
- _Sex Lies and Video Tapes_ | 冲着 James Spader 去的，没想到也被整个片子吸引了。 8/10
- _Ghost Buster 2021_ | 非常一般，除了小姑娘其他的丝毫没有亮点。 5/10

\#3

这周因为封控导致各地都爆发了一些抗议活动，在乌鲁木齐火灾之后进一步到达高潮

虽然不知道未来如何，但是觉醒的人越来越多是个好事；另外就算不是所有抗议的人都是此前为之努力的，现在被作为可团结力量也是一个好的开始。

这里还一句腊主席的名言：星星之火可以燎原！

## Work

\#1

本周学习进度有点超出预期。

看了两个 CppCon talk：
- _CppCon 2020 | Performance Matters - Emery Berger_
  这个 talk 没有给出一些 perf 相关的 insights，实际上是在推销两个 profiling 的工具 szc (stablelizer）和 coz（causal profiling）
- _CppCon 2020 | Pipes: How Plumbing Can Make Your C++ Code More Expressive - Jonathan Boccara_
  这个 talk 是在推销作者自己写的 pipes lib，一个类似 ranges 但是采用 push model 的库
  个人认为这个 talk 最精华的大概就是前20分钟，让我知道了 ranges 采用的 pull model 在一些情况下容易踩出性能问题；以及 ranges 目前不能对 rvalue 使用

两本书：
- _Software Engineering at Google | 16. Version Control and Branch Management_
  看了快20页，估计下周能看完。前面 VCS 介绍的比较多，这部分必要性一般。G家自己使用的策略值得一看，不过采用前最好思考一下自己的业务、团队配置是否适合。
  对我个人， No long-lived branches 和 one version deps 本身是比较合我胃口。
- _大规模分布式存储系统 | Chapter 9 分布式存储引擎_
  看了也有差不多15页，算是有点点深入的介绍了一下 oceanbase 几个核心模块，是前章节 oceanbase 整体架构的进阶篇

看了一篇 post:
- [Post Amazon S3 Availability Event: July 20, 2008](https://github.com/kingsamchen/footnotes-of-linux-multithreaded-server-side-programming/blob/master/amazon_s3_availability_event_july_20_2008.md)
  Amazon S3 当年因为网络通信单比特错误导致 gossip 通信把整个集群状态都给弄乱了导致的事故

\#2

这周自己写了一个 `source_location`，是打算在公司的 codebase 利用的。

某些时候再内部返回一个 `absl::Status` 时光有 error message 还不够，因为调用层次一旦超过3层，就不太容易定位是哪层返回的。

不用 `std::source_location` 是因为我们目前还只支持 C++ 17，所以只能自己糊一个。并且因为没有 `consteval` 所以严格来说也不是完全符合 proposal 的。

\#3

另外这周研究了一下如何让 Makefile 支持头文件变更时自动编译关联的源码文件。

虽然网上的资料很多，但是大多都是坑。

淘了半天终于找到两篇有点内容的：

- https://gist.github.com/maxtruxa/4b3929e118914ccef057f8a05c614b0f
- https://make.mad-scientist.net/papers/advanced-auto-dependency-generation/

第一篇其实是第二篇的一个调整。

不过我在试验过程中发现如果直接用，会出现 `.o` 不会被重复编译，但是最终 `.so` 或者 executable 会被重复链接的情况。

第一篇的也有好几个人提了这个问题，作者也没能解决，还回了一句：For future readers, please save your trouble just use CMake...

我也想用 CMake 啊，这不是拍板权不在我这嘛...

不过还好最终大力出奇迹，把 `%.o` 的依赖做了调增，去掉了必须要先生成 `.d` 的要求。

代价是生成的 `.d` 得在下一轮才能被用上，不过我想了一想好像这也不算啥问题？

实在不行还有 `make clean` 兜底呢

---

好了本周就是这样，下周见
