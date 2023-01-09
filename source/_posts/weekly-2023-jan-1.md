---
title: 一周杂记 in Week 1 Jan 2023
categories: CODE-LIFE
date: 2023-01-09 00:21:39
tags: [杂记]
---
本周是2023年一月的第一周。Happy New Year！

## Life

\#1

过去一周全组基本都在家办公，距离上一次去办公室估计都有20天了。

目前想法是春节前都不回办公室了，第一是因为大部分同事都不回去，一个人回办公室连饭都不知道上哪吃；第二是元旦后回办公室办公需要全程带着口罩，感觉太折腾了；第三点最重要的，自从知道不仅调薪全无，连年底的年终可能都要没有的消息之后，基本没有啥动力了。

虽然我不咋在乎这些钱，但是过去一年的工作没有啥 recognition 也太蛋疼了，尤其 US 还蹭了最后一波福利...

\#2

最近股市债券和理财都有向好回血的趋势。

没啥其他新想法，就希望这个趋势继续保持吧...

主要对总输寄亲自抓经济完全没有任何积极想法...

\#3

这周看了

- Morbius 演员很努力，但是其他方面实在太差了... 5.5/10 不能再多
- Knifes Out 2: Glass Onion 7.5/10 除了前面铺垫有点久之外其他的还好；关键媳妇儿很喜欢，觉得比1都好；她觉得1太教科书了，没有啥惊喜
- 美剧 Terminal List 我比较喜欢这类暴力的动作剧集，目前看了三集。整体上有点惩罚者和24小时后面几季 Jack 黑化的综合感。

## Work

\#1

周五发现新部署在 staging/alpha 上会导致我们的 MTA 一直 signal 11 coredump，同 US 的 leader 差不多查了一天的 root cause。

因为一些特殊原因导致一开始 Postfix 没法生成 coredump file，所以一开始只能通过开启 verbose log 来猜测。

一度以为是我的 MR 改动引起的，最后根据日志感觉是挂在 storage layer 上，并且感觉是函数 A 或者它内部调用导致的问题。

后来 Leader 想到 dmesg 可以查看有限的 coredump 信息，证实了我们认为 core 在 storage layer 的猜测。最后一顿操作终于把 coredump file 搞出来了。

有了 coredump 查问题就容易多了，最后发现是另外一个同事顺手一个优化/重构没注意到另外一部分代码，直接传参穿了 nullptr 给写挂了……

\#2

另外一个有意思的问题是因为某个 task 需要，研究了一把 top-level domain 和 effective second-level domain。

我需要输入一个 domain，返回它的 ESLD，例如输入 `www.google.com` 输出 `google.com`。

这个并不是一个 trivial 的问题，因为 `www.google.co.uk` 的 ESLD 是 `google.co.uk`。

研究了一下发现有一个 public suffix data file 记录了当前所有的 top-level domain，可以作为数据源。

本来想是不是要自己写相关算法代码，syncup 的时候 Head 说有个现成的库 libpsl 可以考虑一下。看了一下 doc 发现果然可以直接用，而且这个库内部也用了 public suffix data 作为数据源。

中间又抽了一点时间稍微过了一下 libpsl 的源代码，大概明白了它的核心实现。毕竟 backend codebase 要引入三方库还是要至少懂他们的 ins-and-outs

\#3

本周学习进度

- 把 https://beej.us/guide/bggdb/ 看完了，所以 GDB 入门的资料差不多就过完了。自己也做了 notes & cheatsheet 可以随时翻查
- Database Internals 看完了 7. Log-Structured Storage 和 8. Distributed System Introduction and Overview
  LSM 这章还是看的我一脸懵，第八章开始时分布式系统部分；就这作者的显摆式内容，要不是我提前知道分布式系统的小九九，一样给我看的云里雾里
- Software Engineering at Google 看完了 20. Static Analysis 和 21. Dependency Management
  依赖管理有点意思；正经公司的大 codebase 管理依赖和需要的方式和 OSS 真的是截然不同
- 极客时间课程刷完了 从0开始学微服务
  有一些收获，但真正的干货不多，实际上还是比较鸡肋

还有两个视频：

- Talk Aync Part 1
  这个是对 ASIO 作者的采访，核心内容是 C++ 20 的 coroutine 对网络编程的影响。talk 质量很高，值得观看。
  下周会把 Part 2 也看完
- CppCon 2020 | Template Metaprogramming: Type Traits (part 1 of 2) - Jody Hagins
  非常好的入门内容；下周同样会把 Part 2 刷完

---

好了这周就这样，下周见
