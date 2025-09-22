---
title: 一周杂记 in Week 3 Sep 2025
categories: CODE-LIFE
date: 2025-09-22 21:45:38
tags: [杂记]
---
本周（09/15 ~ 09/21）是9月份第三周。

本周气温开始有下降的趋势

## Life

\#1

这周 review 了一下手头 workstream 的进度，发现 CN/HZ 这边的进度还是非常及时的，但是 US 那边就真的是一言难尽了。

US 那边的人真的是水平又菜又事逼，做事情的质量也差，让他们细致的干点活真的是太费心费神了。

现在就想着赶紧把 lead 的这个 workstream 做完，赶紧让我变成一个不用操心别人的小IC就好了。

毕竟还有很多核心的未来重写需要的核心基础框架还没写完呢。

\#2

上周马总说他被滴滴毕业了，所以这周赶紧趁着他还没离开杭州找他吃了顿饭。

本来说是请他吃饭的结果变成我硬蹭了一顿饭。

不过选的鹤岗烧烤是真的不咋好吃，虽然这家店宣传广告还行，但是去饭店核心还是去吃不是...

看看马总后续的规划情况，毕竟我觉得再过4-5年可能我也要考虑这个了 🤔

\#3

这周老婆给娃买的地垫到了，整体品质还可以，虽然有一点点硬

娃躺在上面还挺舒服，虽然摩擦太多容易把皮肤擦红；但是现在娃现在特别喜欢翻身和趴卧，所以有这个地点比较合适他发挥，而且这个地点清理起来很方便，口水和吐出来的奶都比较容易擦。

周六周日两天傍晚都带着娃下楼转了转，娃真的喜欢楼下的感觉啊，适逢这周明显降温，傍晚下楼吹吹风呼吸一下新鲜空气还是很舒服的。

周六晚上和媳妇儿去了滨江龙湖天街吃了个云南菌菇火锅，实话说菌菇味道还不错，虽然不知道这个汤底有多少科技含量，但是整体上我还算比较满意。

但是肥牛阿，配菜阿，饮料阿，都不太行。整体有点高开低走的样子吧。

\#4

这周电影俱乐部看了

- 电影 **与鬼怪们的平静生活 Bodegón con fantasmas** 3.5/5 荒诞讽刺的鬼怪小故事 但是后劲不如蛮荒故事


## Work

\#1

**CppCon 2023 | C++23: An Overview of Almost All New and Updated Features - Marc Gregoire** https://www.youtube.com/watch?v=Cttb8vMuq-Y&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=4

- 快速概览，适合上手

**CppCon 2023 | std::linalg: Linear Algebra Coming to Standard C++ - Mark Hoemmen** https://www.youtube.com/watch?v=-UXHMlAMXNk&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=3

- NV 一堆人搞得 BLAS 的标准库实现，应该会进入 26
- 不是这个领域的，随便喵一下就行了

**C++ Weekly - Ep 268 - Top 10 C++ Resources You MUST Know About!** https://www.youtube.com/watch?v=eSDVVrjFh54

- 介绍一些学习资源

**C++ Weekly - Ep 280 - A Quick Look At The Source To Amazon's O3DE Game Engine (constexpr surprises!)** https://www.youtube.com/watch?v=IndGlm2uZCU

- 赏析开源游戏引擎代码
- 咋说呢，只要能挣钱就是好代码

**C++ Weekly - Ep 285 - Experiments With Generating Stably Random Game Assets** https://www.youtube.com/watch?v=xMdwK9p5qOw

- 介绍的一个快速随机数生成算法，xoroshiro
- 看实现确实很快，只要两个初始种子就行 https://nessan.github.io/xoshiro/

**C++ Weekly - Ep 286 - How Command and Conquer's Dual Screen DOS Support Worked** https://www.youtube.com/watch?v=wDvEzmEurlQ

- 考古系列

**C++ Weekly - Ep 497 - How to Add Static Analysis to Legacy C++** https://www.youtube.com/watch?v=7_nSywhw_E8

- 核心就是慢慢来，逐步演进…

**How to Measure String SSO Length with constinit and constexpr** https://www.cppstories.com/2022/sso-cpp20-checks/

- 折腾半天是说怎么判断某个实现的 std::string 的 sso 可容纳字符数量…
- libc++ 的最多，可以存21个；MSVC 和 GCC 都是 15

**Exploring the Limits of Class Template Argument Deduction** https://www.lukas-barth.net/blog/exploring-limits-ctad/

- 搞出了一段 CTAD 搭配 SFINAE 的代码会让 clang 前端不能正确按照标准处理
- 看了一下这个 issue 挂了几年了还在呢…

**C++ Move Semantics Considered Harmful (Rust is better)** https://www.thecodedmessage.com/posts/cpp-move/

- 文章配色太费眼睛了，后半部分直接快速扫描了一下就过了。核心就是 c++ 是 non-destructive move，存在 use after move 的风险
- 至于其他的性能啥的基本都不没在要害上，reddit 上也有讨论 https://news.ycombinator.com/item?id=33702435
- 结论就是开 ubsanitizer 和 clang-tidy 呗

\#2

这周还在研究怎么封装 query params，因为 boost::url 这个库有一丢丢奇怪，所以在这方面需要有不少时间的实验和适配。

另外本来考虑 fawkes::request 应该不提供 mutable operations，但是一方面如果这样做的话，一些接口的实现会很别扭，例如 path params 如何加入，另外一方面是如果一些 middleware 希望对 request 做一些“调整”，没有 mutable operations 就没法搞这个。

所以后面一点点看吧。

---

好了这周就这样，下周见
