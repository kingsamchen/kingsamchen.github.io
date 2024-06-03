---
title: 一周杂记 in Week 5 May 2024
categories: CODE-LIFE
date: 2024-06-03 23:36:47
tags: [杂记]
---
本周（05/27 ~ 06/02）是五月份最后一周，周末就是6月份开始了。

## Life

\#1

这周周一中午十点四十多在家吃了饭之后，十一点出头就开车送媳妇儿去富阳，然后中午又开回来继续上班

周三下午请了两三个小时假开去富阳接媳妇儿回家，顺带去水晶城吃了一顿九田家

周四照常上班，晚上一下班就回家和媳妇儿一起吃了顿萨莉亚，可惜遇到下大雨，本来打算去看附近不远处一个小区媳妇儿发现的一窝猫，但是雨太大就作罢了

周五一大早，大概6:50左右，送媳妇儿去富阳。7:50多到的，下着不大不小的雨。因为这个时候刚好上班早高峰，回去的话虽然能赶上9:30开会，但是路上估计要堵的难受，所以带了笔记本在富阳媳妇儿的值班室开始上班。

十一点半下班刚好媳妇儿的中饭也送到了，吃了个中饭歇了十几分钟就回家了，到家还没到一点。但是这雨还是挺大的，去公司骑电驴感觉有点难受，所以就决定还是在家办公了 🤣

周六早上9点出发再去富阳接媳妇儿，这次总算是结束了媳妇儿为期俩月的富阳校医之行 🤣

这一周开了差不多400公里，还好是电车，随便造

\#2

本周观影

- **长城 The Great Wall** 1/5 “他们给的实在太多了”
- **狂飙** 开始看，暂时只打算在电脑上看
- **Point Break** 开了一个头，但是总感觉不是我喜欢的那种类型，所以看的断断续续...

## Work

\#1

本周学习

**CppCon 2021 | Software Engineering Is About Tradeoffs - Mateusz Pusz**

- gcc/llvm/folly 三家 SSO 的 tradeoff
- 虽然 STL 容器的大多数操作可以做到 strong exceptioni safety，但是 std::varaint 的 valueless exception 只能做到 basic exception，需要自己 catch exception 之后 assign/reset varaint 值；boost.varaint 为了做到 strong exception safety，需要用额外的 heap storage 来存储 backup value
- 然后画风有点奇怪开始讲（可能是作者自己实现的）user defined literal 的科学量纲在实际中的一些使用的 tradeoff
- 前两个 part 还是有点意思的

**CppCon 2021 | Cool New Stuff in Gdb 9 and Gdb 10 - Greg Law**

- 用 objcopy / strip 分离 executable 和他的调试符号
- debuginfod
- 一些 user defined commands 的trics
    - `| CMD | shell` 这个感觉挺不错的，例如 `| info sharedlibs | grep xxx`
    - `set print frame-arguments all | none | presence | scalars`
    - `set print frame-info ...`
    - …

**My tutorial and take on C++20 coroutines** https://www.scs.stanford.edu/~dm/blog/c++-coroutines.html

- 这篇稍微微有点长，花了点几天才断断续续看完的
- 比较适合作为 c++ coroutine 的入门文章，coroutine_handle, awaitable, promise_type 这些都涉及到了，并且是一步一步的深入
- co_yield 和 co_return 也有比较深入的分析
- 最后的 generator sample 可以单独列出来学习，顺带配合 https://github.com/TartanLlama/generator 这个一起

\#2

认真过了一遍 _my tutorial and take on C++ 20 coroutines_ 这篇文章最后的 generator，然后发现 cppreference 上有几个示例几户和它长得一样…

然后自己也动手（默）[写了一遍](https://github.com/kingsamchen/Eureka/blob/master/learn-cxx-coroutine/simple_generator/main.cpp)

\#3

这周抽了点时间把 ubuntu 的 clang/toolchain 升级到了 v18，然后不出意外的有一些新的 tidy complaints

于是就又花了一点时间把 esl 的 tidy issues 都给清了，顺带做了一点其他的小调整，见 PR：https://github.com/kingsamchen/esl/pull/17

---

好了这周就这样，下周见
