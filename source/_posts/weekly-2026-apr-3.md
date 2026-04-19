---
title: 一周杂记 in Week 3 Apr 2026
categories: CODE-LIFE
date: 2026-04-19 23:04:07
tags: [杂记]
---
本周（04/13 ~ 04/19）是四月第三周，这周二回新办公室，没两天又因为空气质量问题又可以继续居家了 🤣

## Life

\#1

这周二去了新办公室，第一天下着雨又因为比较远有6km，电驴感觉不是很好所以最后时刻选择开车去，停在了公司附近的文体中心。

新办公室看起来确实高大上一些，但是，装修的气味有点大，就算几个净化器一起工作也仍然没法显著清除空气中的异味。

而且我们这一层是和别家公司一起合用的，连卫生间都在办公室门禁外...而且这次卫生间只有三个坑位三个小便池...这下感觉自己更外包了。

唯一一点好处是，楼下就有一家 manner，带上自己的杯子一次可以少5块钱，喝四次就赚了一杯 🤡

周三下班和suyang一起回去，因为我们路线完全重合，我到家后他还要起一段，掐表算了一下，走大路路况好等灯不多的情况等下，电驴差不多要22分钟左右，也还能接受。

不过因为办公室气味实在太大，最后公司同意了如果有需求可以继续居家办公，所以周四开始又居家了。而且目前可以一直居家到 5.1 假期回来 🫡

\#2

周二晚上 suyang 请我们几个吃饭，选的是一家衢州菜，我开车带着蔡司令和他媳妇儿还有 jed 到了店，那个停车位置确实有点尴尬，绕了三圈才找到停车场入口 🤡

衢州菜的风格就是，前两三盘菜会觉得味道不错，然后吃着吃着就感觉一直在复制粘贴了，没有啥味觉变化

不过到底是蹭了一顿饭，也还是不错的。

\#3

上周说马桶没修好还是漏水，所以这周一师傅又重新来了，取出了去年换的皮圈看了半天也没发现哪里有损坏，于是冲洗了一下又放回去了。

然后说因为今天没带皮圈所以让我观察一下，如果还漏水再打电话给他，他再上门换皮圈。

结果没想到连续观察了两三天居然就不漏水了，就非常 interesting

所以实质上确实给修好了 🤔

\#4

这周日和周一晚上都严重失眠，分别到凌晨5点和凌晨四点才睡着，而且周日晚上吃了最后一粒褪黑素，感觉反而更精神了...

不幸中的万幸是虽然分别睡了3个多小时和四个多小时，深度睡眠都有超过1个小时，所以第二天都没那么要死要死的状态。

周二到了新办公室之后立马下单买了新的褪黑素

周二晚上间隔吃了两粒，终于给压下去了，睡了个好觉。

后面几天没吃也能算进入比较正常的睡眠了。

\#5

时隔俩月，周日终于又上了一节拳击训练营。

因为太久没有这种强度的运动，周日这次真的是快给累死了，打完手都在发抖 🤣

以后还是得保持一周一次的强度，否则这个起伏反而影响身体强度和恢复。

\#5

本周两部电影都是晚上自己带着耳机看的

- **伸冤人3 The Equalizer 3‎** 3.5/5 反派太弱了 撑不起这集
- **秘密会议 Conclave 4/5** 色彩打光镜头讲究 剧本上马马虎虎 费叔撑起半边天

## Work

\#1

**CppNow 2025 | Implementing the C++ Standard Library Proposal for any_view - Patrick Roberts** https://www.youtube.com/watch?v=w9XMpVBkCvM&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=45

- 没看懂，不知道这个东西的使用场景的是什么
- 因为有 type erasure 所以里面还有针对 virtual function call 的优化，因为频率挺高

**CppCon 2023 | Lightning Talk: Let's Fix Sparse Linear Algebra with C++. It'll Be Fun and Easy! - Benjamin Brock** https://www.youtube.com/watch?v=vhpj-pQTJPA&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=36

- 利用 ranges 提供的 adaptors 来实现 csr_matrix 这种 views/adaptors

**CppCon 2023 | How to Build Your First C++ Automated Refactoring Tool - Kristen Shaker** https://www.youtube.com/watch?v=torqlZnu9Ag&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=35

- 展示了如何自己写 tidy checks
- 但是对于我们来说，如何分发这个新的二进制才是大问题啊

**C++ Weekly - Ep 528 - Protecting From Fallthrough** https://www.youtube.com/watch?v=qcWC2yF0L7c

- 用新点的编译期，一般都会告警，可以用 `[[fallthrough]]` 来告诉编译器就是要 fallthrough
- 另外一个做法是每个 case 之后用 return，但是我觉得你都用 return 了怎么记不住用 break 呢？

**C++ Weekly - Ep 160 - Argument Dependent Lookup (ADL)** https://www.youtube.com/watch?v=agS-h_eaLj8

- ADL 介绍

**C++ Weekly - Ep 159 - `constexpr` `virtual` Members In C++20** https://www.youtube.com/watch?v=JXJg_XMJFW0

- C++ 20 开始 virtual functions 也可以是 constexpr 了
- 这个是真的看了才知道，震惊中

**C++ Weekly - Ep 158 - Getting The Most Out Of Your CPU** https://www.youtube.com/watch?v=_4D1y_KyEzA

- 编译优化指定 cpu arch 来生成更贴切的 simd 指令

**C++ Weekly - Ep 157 - Never Overload Operator && or ||** https://www.youtube.com/watch?v=hCGadTsT0S0

- 重载 logic operators 会导致 short circuits 失效

**Argument-Dependent Lookup and the Hidden Friend Idiom** https://www.modernescpp.com/index.php/argument-dependent-lookup-and-hidden-friends/

- 介绍 ADL 和 hidden friend idiom，但是我觉得这篇文章没有点到 hidden friend 的精髓，即函数只能被 ADL 找到，不能被直接调用，见 https://gcc.godbolt.org/z/nqT4Waxr6

**How to make chunks of a range in C++23** https://mariusbancila.ro/blog/2023/01/16/how-to-make-chunks-of-a-range-in-cpp23/

- 如何利用 cpp ranges 把一个数据源的数据按照 chunk 来处理，有各种策略支持

\#2

说到之前给 chao 打黑工之一的帮 scheduler 迁移到 conan packages 上，因为 Makefile 自身实在太蛋疼了，导致就算有 pkgconf 搞起来也是很难受，遇到了几个坑之后觉得解决方案都不是很干净，所以这周索性直接切 CMake 了，而且选择直接上静态链接，毕竟 conan packages 默认都是开了 `-fPIC`

不过你别说，进度比预期快，借助 cursor/opus 很快就把进度推到了之前的样子，而且使用体验好了许多

不过因为静态链接要求更高，直接暴露出了问题，踩了一些 conan package 打包的时候的一些坑，例如没有 transitive headers .etc，多亏了 opus 这个强力模型，好多问题都是快速定位解决掉饿了。

另外构建机锁死在 conan 2.0.13 上，但是消费端其实是可以用新的 conan的。

不过这里也遇到一个大坑：老版本 conan 不认/自动忽略 aws sdk 中的 languages=C，但是新 conan 又会识别这个 attribute，导致 aws系列的包本地和远端的 pacakge id 对不上，这里又多亏 cursor 给我迅速找到问题，直接把这部分注释之后冲洗打包就行。

后面要考虑一下是不是把 mongo 的依赖也给一起弄了，因为按照进度 scheduler 估计很快就要开始推 mongo 迁移了；这部分主要有一些依赖又要先走 z3pc 很麻烦，而且构建镜像需要装 libmongocrypt 这个还得找 build team 干 🫠

不过直接上 CMake 这个体验是真的好太多了

---

好了这周就这样，下周见
