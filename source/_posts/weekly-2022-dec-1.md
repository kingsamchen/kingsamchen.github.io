---
title: 一周杂记 in Week 1 Dec 2022
categories: CODE-LIFE
date: 2022-12-04 22:37:58
tags: [杂记]
---
本周是12月第一周，离2023年只剩下最后一个月了。

## Life

\#1

本周花了不少时间看电影，合计4部。

- _Gunpowder Milkshake_ 5.5/10
  感觉有点在仿 John Wick 系列，但是各方面都差别很大。有点过分在女权道路上走偏了。
- _Athena_ 6/10
  前面都感觉还行，难得没有用力过猛，但是最后的结尾完全败笔，私货夹得多了。
  同事说这部不如同为法国电影的《悲惨世界》（不是古典名著的那个电影）
- _The Outfit_ 7.5/10
  一个老裁缝利用嘴炮借刀杀人。剧情精巧，转折出乎意料又在情理之中。如果能在深挖一点就更好了
- _In the Same Breath_ 8/10
  描写新冠（2020 ~ 2021）大流行的记录片，从平民视角出发。这个时间点看这个片子又有一种不同的感受。

这周本来打算和媳妇儿一起看子弹列车，但是因为媳妇儿整理硕士答辩资料的进度不及预期，所以又要延期到下周了。

\#2

上周接二连三的示威游行白纸运动之后，这周有点超出意外的防疫口风有所松动。

开始宣传暂时没有迹象表明 omicron 有后遗症和 omicron 毒性大大降低的新闻多了起来。

很多城市也开始宣布室外公共场所和公共交通不需要再查看核酸。

不确定这是一个引蛇出洞还是真的是一种妥协？

而且最近杭州病例数在明显增加，不知道会不会倒在黎明前？

\#3

这周另外一件大事情是 +1s 再也没有用了，因为长者确认离世。

很多人对他的评价功过参半，但是不管怎么说，他执政的十年和垂帘听政的后十年是发展最快的二十年。

全靠同行衬托，只能说包子实在太拉跨了。

## Work

\#1

本周学习进度

- _Software Engineering at Google | 16. Version Control and Branch Management_ 完结
- _Software Engineering at Google | 17. Code Search_ 看了十几页
- 大规模分布式存储系统 | Chapter 9 分布式存储引擎 完结
- 看了两篇和 ethernet crc & tcp/ip checksum 有关的 posts
  - When The CRC and TCP Checksum Disagree &  That Couldnt Happen To Us...Or Could It?
  - https://github.com/kingsamchen/footnotes-of-linux-multithreaded-server-side-programming/blob/master/the_limitations_of_the_ethernet_crc_and_tcpip_checksum_for_error_detection.md
- 继续开了一个极客时间的新专栏：从零开始学微服务
  刚好我们自己的项目也面临着微服务化改造，所以拿过来看一下找找灵感
- _CppCon 2020 | Practical Memory Pool Based Allocators For Modern C++ - Misha Shalem_
  适合限定环境（机器人，嵌入式等）下的内存分配器
- _CppCon 2020 | Quickly Testing Qt Desktop Applications with Approval Tests - Clare Macrae_
  Qt 客户端程序如何开展自动化测试
- 看了一篇讲 cuckoo hashing 的论文

\#2

抽了个时间给 anvil 做了一个小改进。

顺带研究了一下 GitHub 上锁定 master 提交，让新提交只允许通过 Pull Request 的方式进入 master。

感觉一些设定上和在公司用的 GitLab 有不少区别。

\#3

这周帮同事看一个 task 时发现一个 JSON 的坑点：JSON 标准要求出现 `\` 一定要转义，这对应到 nlohman-json 在调用 `json::dump()` 时一定会对 `\` 转义。

而我们希望将包含 `\u003c` 这个 unicode point 按照字符串形式 dump 出来传输给对端。于是遇到两个问题：

1. 如果使用裸 unicode point，例如 `\u003c` 在 dump 时会被自动转译成对应的 ASCII 码，这不是我们希望的。
而且虽然 nlohmann-json 允许 dump 时保持 non-ascii 数据以 unicode point 展示，但是我们的数据恰好是 ASCII 字符，而我们又不希望他被自动 interpret
2. 如果以 string literal 存储，即 `"\\u003c"` 或者 `R"(\u003c)"`，因为前面提到的转义规则，对端接收到的内容会真的变成 `\\u003c` 这里有两个 `\\`

研究了好久一直找不到优雅解决的法子，无奈只能说要不考虑一下 workaround 好了...

---

好了本周就是这样，下周见
