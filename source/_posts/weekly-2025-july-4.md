---
title: 一周杂记 in Week 4 July 2025
categories: CODE-LIFE
date: 2025-07-28 23:23:05
tags: [杂记]
---
本周（07/21 ~ 07/27）是7月份的第四周。

## Life

\#1

灾后重建第三周，这周开的会比之前一个月还要多，而且还有好多会都是大早上爬起来8点钟给美国那边开，导致这周睡眠严重不足。

另外周四早上大老板 Adrain 从罗马尼亚回来之后做了第一次 Scrum of Scrums（SoS，怎么都这么喜欢玩缩写）的汇报，但是因为人家的路线这边的 manager 没有搞清楚，过去几周都是这边 manager 自己偷跑的事情，所以对面对我们的工作进展不是很满意。

会后给了一个 doc template 要按照模板来写汇报文档。我就是想说，这模板怎么这么像 RCA 的文档啊，而且里面还真有一段 root cause analysis...

最近真的不是在开会就是在写汇报材料，别说自己的代码，连公司的有些代码都没来得及写，这日子真的太操蛋了。

\#2

上周日晚上和老婆喝了一杯古茗，结果因为咖啡因爆表直接导致周一就没睡好

周四带屎总去了滨江的街道卫生院打了第二次疫苗。

滨江真的是有钱多了，这个街道卫生院非常的先进，留观还有倒计时；母婴室也很专业，停车场不超过一小时还免费停车...

\#3

这周本来说好了把家里的电视机搬到老婆家，调查完一圈连顺丰都约好了，200定金都付好了，最后因为老婆和丈母娘太傻逼了，非要在支架增加电视和墙壁的间隙厚度间纠结。直接让我受不了阴阳了一顿，然后她们就不干了。

算了，真傻逼，都不想浪费时间说这个了。

## Work

\#1

**CppCon 2022 | New in Visual Studio 2022 - Conformance, Performance, Important Features - Marian Luparu & Sy Brand** https://www.youtube.com/watch?v=vdblR5Ty9f8&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=114

- 如题，介绍 VS 2022 的改进，看起来标准跟进的还可以，asan和 fuzzsan 都支持了
- IDE 自身功能也有改进，不过现在真的很少在 Windows 上直接写代码了。。。

**CppCon 2022 | Reproducible Developer Environments in C++ - Michael Price** https://www.youtube.com/watch?v=9GKGp3zeOOc&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=98

- MSFT 搞得 dev container

**C++ Weekly - Ep 307 - Making C++ Fun and Accessible** https://www.youtube.com/watch?v=3RskKe7I6T4

- Jason Object Lifetime Puzzle 一书的宣传广告 🤣

**C++ Weekly - Ep 308 - 'if consteval' - There's More To This Story** https://www.youtube.com/watch?v=y3r9l3LZiJ8

- 介绍 `if consteval` 以及建议不要使用 `std::is_constant_evaluated()` 因为后者很容易误用
- 和之前提到的 https://www.sandordargo.com/blog/2022/06/01/cpp23-if-consteval 这篇博客内容类似

**C++ Weekly - Ep 309 - Are Code Comments a Code Smell?** https://www.youtube.com/watch?v=8V6Ry5eTTcc

- 举了几个可以用更好的函数名或者代码逻辑来取代注释
- ⇒ 某些注释是不是说明对应的代码需要调整

**C++ Weekly - Ep 310 - Your Small Integer Operations Are Broken!** https://www.youtube.com/watch?v=R6_PFqOSa_c

- 因为存在 promotion 的 implicit conversion 的问题，所以小整数操作上很容易有坑
- 核心结论就是建议打开 `-Wconversion` 或者 `/W4` 告警

**C++ Weekly - Ep 489 - C++11's User Defined Literals** https://www.youtube.com/watch?v=DU1SmsjUxWg

- quick introduction to user defined literals
- 不过看了这期才注意到 UDL 中如果是 integer literal 只能用 unsigned long long，如果是负数，那么结构体自己还要定义 `operator-()` 这个操作符因为 `-42_bigint` 会被编译成 `-bigint(42)`

**Thoughts on -Wctad-maybe-unsupported** https://quuxplusone.github.io/blog/2022/10/07/wctad-maybe-unsupported/

- 这篇很有意思，作者比较反对 ctad 这个 feature，他认为没有 deduction guides 的话默认应该不匹配；目前 gcc clang 会有一些 may not supported 的告警，但是作者觉得程度还不够

\#2

这周日给 fawkes 设置了 GitHub Actions，一不小心踩了好几个坑，差不多被坑了一天才把 GitHub CI 设置好。

第一个坑是 clang-tidy 默认开了 `-clang-analyzer-*` 的规则，然后跑 tidy 的时候发现把 include 进来的 asio 的 hpp 里的代码给标记了，蛋疼的是这个规则比较特殊，.clang-tidy 里的 `HeaderFilterRegex` 和 `ExcludeHeaderFilterRegex` 对这个都没有效果。

研究了好久发现这个问题绕有绕不开，想用其他的 alternatives 续接上也不是很好搞，最后索性不管了，直接把这个 check 给删掉了。

第二个坑是 clang 编译的时候，vcpkg 用的 fmtlib 是 11.0.2，这个版本有bug，spdlog 使用的某段代码会编译失败...

还好这个问题之前遇到过一次了，所以解决方案就是用自己的 overlay 把 fmtlib 直接拉到了最新版本。

至于为啥 vcpkg 至今没有把 fmtlib 版本升级到最新呢，我估计是和其他的lib的配合没那么容易，尤其 fmtlib 现在是相当多的库的依赖库...

第三个坑是排查的最痛苦的坑。

用 clang 编译的时候如果解决了上面这个问题，还会遇到 `find_package(Threads REQUIRED)` 失败的问题，也就是说特么在 Ubuntu 22.04 上找不到 libc pthread...

最后意外发现原因是我只改写了环境变量 `CXX=clang++`，只要我顺带 `CC=clang` 这个问题就解决了...好吧

第四个坑就比较简单了，因为 clang 的job只安装了 clang 没有安装 clang-tools 所以编译链接中间缺了一些工具，失败了。

所以只要 apt install 补上就行了

完整的 PR 可以看 https://github.com/kingsamchen/fawkes/pull/1

---

好了这周就这样，下周见
