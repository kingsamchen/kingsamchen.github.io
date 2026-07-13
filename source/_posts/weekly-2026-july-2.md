---
title: 一周杂记 in Week 2 July 2026
categories: CODE-LIFE
date: 2026-07-13 22:11:11
tags: [杂记]
---
本周（7.6 ~ 7.12）是7月份的第二周，这周事情有点多。

## Life

\#1

这周周一的时候发现周末做的 security journey 做错了...按照周一下班结束前要做完的 2026 refresher 我还剩了十来个课其中有一半是实操的还没做

霎时感觉人要没了，于是牟足了 AI 赶紧疯狂做课

还好大部分实操课都比较二，所以其实做起来也还好，所以到了下午三点多的时候也都做完了，长舒了一口气...

因为周六上午要迁一张产线的表到 mongo，所以早早起床8点就坐在电脑前了。

而且因为我的顺序不是特别靠前，所以等了半个多小时才轮到我。

还好我的迁移比较顺利，ZTP也都顺利跑完了，最后还捞了1个小时的加班时间。

\#2

周五上午开车带老婆去紫金港校区拿博士录取通知书，但是车开到校门口后保安不让进。。。

于是我让老婆先进去拿通知书，而我在外面像驴一样转了两圈，最后停在门口对面一个出租车揽客的地方

不过还好等了十几分钟老婆就拿完通知书出来了，于是又顺利的开回家了。

路上老婆吐槽紫金港离家就3KM不到，但是校门口到医学院楼就接近2KM了，大热天骑个自行车都觉得累 🤡

\#3

因为周末有大台风，所以周五中午的时候老婆说傍晚我早点下班开车带着秋宝去一个湘湖科学岛看大草坪。

于是周五5点就提前下班了，开车载着全家人开了40公里跑到湘湖科学岛。

草坪确实有点大，而且还有好几米高；但是看来看去也就这样，加上天气实在太热，我觉得网红景点的成分超过了风景自身。

而且秋宝可能也被热到了，也不是特别开心，大部分时间都是严肃板着脸。

不过还好逛了一个小时就开车回家了，虽然路上堵了一点点但还是8点出头就到家了，秋宝算是熬了个小夜吧。

\#4

本来周五晚上和老婆要去邓紫棋演唱会的的，结果来了台风直接给取消了，但是周五明明这风平浪静啊。。。

老婆本来想着那就周六晚 tank 的室内演唱会，结果提前一天又通知说取消了。。。

然后到了周六上午的时候，包子说乐刻通知下午开始到周日，团课和训练营都要取消。。。

直接没绷住 🤷‍♂️

## Work

\#1

**CppNow 2025 | A Practitioner’s Guide to Writing std-Compatible Views in C++ - Zach Laine** https://www.youtube.com/watch?v=j2TZ58KGtC8&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=36

- 如何写一个兼容 std::ranges::views 的 view
- 技术细节感觉太多了，如果没有事先做过类似工作的人完全看得云里雾里

**C++ Weekly - Ep 540 - Creating New Types with C++26's Reflection** https://www.youtube.com/watch?v=ZBqTr6YRROA

- 利用 C++ 26 反射可以定义结构 on-the-fly

**C++ Weekly - Ep 118 - Trying Out The vcpkg Package Manager** https://www.youtube.com/watch?v=KOeOLOu6nHw

- vcpkg 介绍
- 不过这是2018年的时候

**C++ Weekly - Ep 117 - Trying Out The Hunter Package Manager** https://www.youtube.com/watch?v=O2_N8OzPGWQ

- 有点类似之前用的 cpm.cmake 不过可能 hunter 不是用的 fetch content 因为那个时候估计还没支持，大概是自己做的一些额外的功能，或者利用 external project add
- 最原始的 https://github.com/ruslo/hunter 已经 2020 年就 archive 了，现在有个还算活跃的 community support https://github.com/cpp-pm/hunter
- 不过严肃项目还是用 vcpkg 或者 conan 吧

**C++ Weekly - Ep 116 - Trying Out The Conan Package Manager** https://www.youtube.com/watch?v=9cCQHJ-cNHY

- conan 介绍，不过是比较老的版本了

**C++ Weekly - Ep 115 - Compile Time ARM Emulator** https://www.youtube.com/watch?v=UYcRvU9UO40

- 用 C++ compile-time 最佳实践写吧写吧搞了一个 arm emulator
- 然后是一些口水话

**The evolution of enums** https://www.sandordargo.com/blog/2023/02/15/evolution-of-enums

- 科普
- 不过有个细节：traditional unscoped enum 的 underlying type 是 implementation-defined 而 scoped enum 不指定就是 int
- 显式指定 underlying type 和 enumerator value 这个需要认真思考

\#2

这周突然被 AT 说 scheduler 6月份的 release 在 oci 的环境连不上 redis，日志一直提示 tls certificate verify failure。

其实我上个周末的时候已经看到这个问题，不过因为周末要忙其他的所以给忘了。

因为相关代码没有变更，所以我第一反应是切换到 conan 之后几个依赖库被升级了，可能升级后行为不一致了。

为了搞清楚是 openssl 的问题还是 redis++ 的问题，结合 aws 和 oci 的区别我让 ops 在 oci 的容器里执行了几个 openssl 的命令，初步得出结论是 peer ssl 证书校验的问题，并且和 openssl 的升级没有关系，因为 cli 用的 openssl 还是老的。

于是搜了一下 redis++ 的 issues，果然找到了相关的 issue：https://github.com/sewenew/redis-plus-plus/issues/183 https://github.com/sewenew/redis-plus-plus/pull/464/changes/5e2af3bee9ccaa285b4ab9ca6869b2d5f0218bbf

我们之前用的老版本的 redis++ 并不会校验对端证书，这个MR之后默认会校验，但是提供了一个 mode 可以跳过校验

但是既然 OCI 环境强制要求走 TLS 而我们又跳过证书校验，就感觉不太对头，于是我在研究有没有其他路子。

又折腾了一番发现是因为 openssl 找不到正确的证书路径导致的，只要给定证书路径就好了。

于是麻利的写了一个 redis_tls 的 executable tool，让 ops 放到 pod 里试一下，验证了确实可行。

插曲：其实一开始以为是因为 OCI 环境我们设置了 internal dns alias 来访问 redis cluster 而这个 alias 不在证书里导致的，但是后来研究下来发现，其实 redis++ 默认的 verify peer 模式并不是最严格的 verification，所以 internal dns alias 并不会导致 verify failure

不过既然 redis++ 实现成这样了就暂时先这样，反正后面得用 boost.redis 替换的 🤡

\#3

这周粗浅的做了一下 scheduler sanitizer support 的准备工作，下周应该要正式开始研究：

1. 如何给 abseil 这样会有 ABI 影响的 conan package 做 sanitizer support
2. 集成后 scheduler 加上 sanitizer

---

这周就这样，下周见
