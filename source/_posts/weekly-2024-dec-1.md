---
title: 一周杂记 in Week 1 Dec 2024
categories: CODE-LIFE
date: 2024-12-08 23:37:04
tags: [杂记]
---
本周（12/02 ~ 12/08）是十二月的第一周，终于进入年末了。

## Life

\#1

张大妈上看到一个品牌短毛毯只要10块钱立马下单入手，洗衣机清水漂洗晾干之后发现质地确实不错，不扎手而且非常保暖。

于是找了一个上午直接铺到之前给小蛋准备的外卖箱改造的窝里了，结果当天晚上就发现小蛋住进了这个给他准备的窝。

看来之前不住确实是因为不够暖和，这小猫还是很聪明的。于是接下来小蛋直接舒舒服服的住在这个大窝里了 😊

最近会隔三岔五的给小蛋换粮换水，专门在他的专有杂物箱放了一个百洁布，用来清洁吃饭喝水的碗，尽最大程度减少细菌滋生。

后面要想一下怎么给这个窝加一个挡雨棚，防止下雨的时候雨水把毛毯弄湿 🤔

\#2

周六傍晚去找小蛋的时候发现边上有比较早之前遇过的小橘，一段时间不见长大了非常多。

给小蛋喂猫条的时候小橘目不转睛地盯着，还冲着我喵喵叫，估计也是想吃，于是就挤了一些给他尝尝鲜。

估计是没吃过过这么好吃的，小伙子立马上头了，叫的更大声了。

于是我又挤了一点给他，没想到这小子太着急又有点害怕，所以直接拿爪子想把毛条勾过去，结果直接挠到我右手食指，给挠出血了。

虽然我知道这种情况感染狂犬病毒的概率近似于无，但是考虑到ROI吃完晚饭做完家务之后还是选择去了红十字打狂犬疫苗。

红十字老地方了，这几年来这里打狂犬疫苗加起来估计都有十几次了；不过这次是自己开车来的，加上是周六晚上交通顺畅，所以单程差不多20分钟就能到。

剩下的就是老套路了，急诊挂号 -> 犬伤科 -> 拿疫苗注射凭条 -> 注射室注射

好消息是这次只需要打两针

最后吐槽一下红十字自己的停车场停车费一小时10块钱，而紧挨着边上的中西医结合医院停车场一小时只需要5块钱，所以我显然直接停在后者了

\#3

本周观影

- **移魂都市 Dark City** 4/5 一些设定上很棒，建筑变换永夜有点朋克感；我为什么是我的主题讨论浅了一些，意念对波这个有点搞

## Work

\#1

**CppCon 2022 | Val: A Safe Language to Interoperate with C++ - Dimitri Racordon** https://www.youtube.com/watch?v=ws-Z8xKbP4w&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=66

- 介绍 val（现在叫 hylo）这个语言的
- 看看就行，不太看好

**CppNow 2024 | Unlocking Modern CPU Power - Next-Gen C++ Optimization Techniques - Fedor G Pikus** https://www.youtube.com/watch?v=wGSSUSeaLgA&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=25

- talk 有点长了，核心是现代新CPU架构下的一些特征和编码上应该遵循的实践做法
    ![](img/unlocking-modern-cpu-power.webp)

**CppNow 2024 | Reflection Is Good for C++ Code Health - Saksham Sharma** https://www.youtube.com/watch?v=GQ5HKL0WRGQ&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=30

- Reflection proposal 可以带来的一些实现上的改进
- 还是处于比较“大家一起来头脑风暴”的状态，还是先等提案 finalized 吧

**CppNow 2024 | An Adventure in Modern C++ Library Design - Robert Leahy** https://www.youtube.com/watch?v=W29fY7Ml4-w&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=32

- 演讲者很有激情，但是太高端了我听完全程硬是没听明白核心点到底是啥，就被各种秀了一脸

**C++ Weekly - Ep 456 - RVO + Trivial Types = Faster Code** https://www.youtube.com/watch?v=DzUAqXMUjtc

- clang 在 nrvo 上表现得比 gcc 要好一些，但是 gcc 上也可以通过把函数拆分来辅助编译器识别优化
- 有 nrvo 的条件下 trivial types 能有 1.5x 的优化空间
- gcc 的 -Wnrvo 可以辅助 trouble shooting（如果有必要的话）

**C++ Weekly - Ep 455 - Tool Spotlight: Mutation Testing with Mull** https://www.youtube.com/watch?v=lhcXAnNgzlo&t=13s

- 介绍 Mull 一个做 mutation testing 的工具
- 这个 mutation testing 是说会把你的程序代码做变换来进行测试，以捕捉潜在的 cornor case…

**C++ Weekly - Ep 430 - How Short String Optimizations Work** https://www.youtube.com/watch?v=CIB_khrNPSU

- 给了一个简化版的 SSO string 的实现

**C++ Weekly - Ep 429 - C++26's Parameter Pack Indexing**  https://www.youtube.com/watch?v=wl7uWes7Sys&t=25s

- C++ 26 就支持 `params...[i]` 这种 syntax 了，不用自己做 recursive expansion 了

**Functions to be called only once in C++** https://www.sandordargo.com/blog/2021/10/27/functions-to-call-only-once

- 这篇很难评，最后给的解决方案我个人认为是不对的，而且最后那个 `getThatCostly()` 也没法保证用户只call一次啊…

**The Self-Growing Builder** https://marcoarena.wordpress.com/2021/10/25/the-self-growing-builder/

- variadic templates + tags + compile-time check tag existence 的做法可以学习一下
- 但是我觉得用途有点迷，builder pattern 设置的不应该都是 options 吗，如果说担心必要参数漏了还能理解，需要放置函数被调用超过一次这是什么鬼要求

**There is no 'printf'** https://www.netmeister.org/blog/return-printf.html

- 考证类文章，没啥意义，大意是说 gcc 编译的时候会把 printf 酌情更换为 puts 换取更好的性能
- 可以用 `-fno-builtin`  来 supress
- https://gcc.godbolt.org/z/x388vvKEG 试了一下确实现在的 gcc 编译 C 代码也会有这样的情况

**Strange behavior with NaN and -ffast-math** https://kristerw.github.io/2021/10/26/fast-math-ub/

- 开了 `-ffast-math` 但是条件不满足，例如确实会产生 NaN, INF 这类数据时，编译器各种优化会把问题变得很蛋疼
- 作者给的建议是就别开 `-ffast-math` 只把他当作人肉检测还有没有优化空间的一个手段

**operator<=> doesn’t obsolete the hidden friend idiom** https://quuxplusone.github.io/blog/2021/10/22/hidden-friend-outlives-spaceship/

- 就算有了 `operator<=>` 也是要定义为 friend function（如果需要访问 private members），因为定义为成员函数的话 `std::ref(x)` 这种是没法调用到成员函数的匹配的

\#2

这周抽了点时间，迈出了第一步，基于 boost.beast + C++ coroutine 写了 http rest server 的一个 poc https://github.com/kingsamchen/Eureka/pull/32

后面会一边熟悉 boost.beast 一边尝试一点点的从 components 开始把一个 rest server framework 需要的部分都给写全了，比如 router。

熟悉 boost.beast/asio + 熟悉 c++ coroutine 并且在这两个基础之上慢慢把 http rest framework 搞出来应该会是未来一段时间的主要 coding tasks 了。

---

好了这周就这样，下周见
