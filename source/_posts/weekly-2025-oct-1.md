---
title: 一周杂记 in Week 1 Oct 2025
categories: CODE-LIFE
date: 2025-10-07 23:58:26
tags: [杂记]
---
本周（09/29 ~ 10/05）是十月份第一周，十一假期终于来了。

## Life

\#1

9/30 的时候又一次（至于为什么是又，看上一篇）带着秋秋去打疫苗。

因为下午时间选的好，所以社区医院人比较少，整个打疫苗的过程还是非常顺利，而且这次秋秋就扎针的那几秒钟哭了一下，那阵痛过了之后居然很快就不哭了，有点牛逼。

在留观期间有个大概3-4岁的小女孩一直盯着秋秋看，还直接跑到我们边上盯着秋秋看，🤣

\#2

这周张杰来杭州连开三天演唱会，老婆给丈母娘买了一张黄牛票，大概原价680买了1400，但是这个黄牛估计是内部工作人员，给的位置非常好，而且票直接出到了媳妇儿的大麦App上。

丈母娘参加完演唱会之后觉得效果和体验非常好，所以老婆就打算第二天4号和一个同事一起去，结果因为这个同事说当天买黄牛票能有更大的折扣。

结果第二天媳妇儿打算买票的时候发现绝大多数票都下架了，好不容易找的一个黄牛也没便宜到哪里去，而且说能不能出票要看下午四点。

又因为媳妇儿的身份证落在拱墅这边，所以我早上就叫了一个SF寄送把身份证给她送了过去。

结果到了下午媳妇儿说没出票成功，演唱会没法去了 🤣

结合前一两天我给她讲的那个挨打吃屎又掏钱的故事，简直直接对上了。

\#3

爸妈4号傍晚到了杭州，我开车到了东站P2停车场后去接他们。

讲道理这个停车场到候车大厅是有点远的，绕着走了感觉得有十几分钟，还好回家路上路比较通畅。

周五周六连续两天一起到媳妇儿家看娃，还一起过了中秋。

娃也没太见外，应该算是适应得比较好。

\#4

6号中午一起吃了中秋饭之后傍晚和媳妇儿还有我姐提前在金帝T-One吃了海底捞，回家之后歇了一会儿就开车去东站了出发了。

老规矩，把车停媳妇儿医院停车场，然后坐一站地铁到东站。

回去是因为7号外婆90大寿的寿宴。

寿宴基本都是很熟的亲戚，外婆也是想趁这个机会和自己尚在的兄弟姊妹一起聚聚。

吃完后到了外婆家坐了一会让就回家收拾行李去车站赶高铁回杭州了。

\#5

单独开一个 section 吐槽一下今年的十一是真的热，平均气温都是33-35℃，也太离谱了。

而且不仅杭州，苍南老家也很热阿。

## Work

\#1

**CppNow 2025 | How to Avoid Headaches with Simple CMake - Bret Brown** https://www.youtube.com/watch?v=xNHKTdnn4fY&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=25

- 来自 bloomberg 的 CMake 使用经验，他们搞了一个 beman 的 project 来 embody 他们认为的 best practices
- 这个是推荐的正经讨论社区 https://discourse.cmake.org/；另外作者提到很多 AI 生成的 cmake snippet 都是非常 old-fashioned 的做法需要引起注意
- 需要定期 bump cmake_minimum_version_required；另外作者觉得 ranged version requirement很鸡肋，不建议使用
- 作者建议对每个需要导出（给别的模块复用的非叶子target）都提供 namespace::module 这种 alias，并且别的模块 ref 的时候都用这个 alias
- 作者还非常推荐使用 header file set 来管理需要导出的对象的头文件（这个我还不熟悉，需要再观察学习一下）

**C++ Weekly - Ep 500 - The Show's Half Over!** https://www.youtube.com/watch?v=Pf7tq3DG88A

- Jason 的 500 期回顾；表示未来会做到 1000 期

**C++ Weekly - Ep 259 - CRTP: What It Is, Some History and Some Uses** https://www.youtube.com/watch?v=ZQ-8laAr9Dg

- 这里用的这个例子，感官上更加突出了 CRTP 适合子类复用基类大多数的实现而且由子类自身提供一些特殊策略的注入

**C++ Weekly - Ep 256 - C++11's Garbage Collector** https://www.youtube.com/watch?v=jZ2pXlcDGFc

- C++ 11 引入的这个不是完整的 GC 而是一个给 GC 操作的 interop 的 layer，告诉 GC 哪些指针是可以/应该/需要被回收的
- 因为 C++ 的 corner cases 太多所以兼容现有实现需要做很多 extra effort；然而实际上没有任何实现用了这个，而且 C++ 23 也会移除这个特性

**C++ Weekly - Ep 257 - Garbage In, Garbage Out - Why Initialization Matters** https://www.youtube.com/watch?v=uYN6-YQPsGo

- local variables 一定要初始化，就这样

**C++ Weekly - Ep 255 - C++11-17 Features You Still Cannot Use in 2021** https://www.youtube.com/watch?v=RpSjU2C5SYc

- 现在这个时间点，除了 gc 那个基本主流的三个都支持了

\#2

这周终于把 fawkes 的 request/response 这部分做完了

PR 见 https://github.com/kingsamchen/fawkes/pull/3

这个 PR 又踩了 lint 这个 pipeline 的坑，主要是 pch 和 clang-tidy 不协调，以及 clang 目前默认会扫描 modules 相关的属性往 compile_commands.json 里写，也会导致 tidy 在没有构建的时候失败。

解决方案就是

```yaml
env:
    CC: clang-20
    CXX: clang++-20
run: |
    cmake --preset linux-release -DFAWKES_USE_SANITIZERS="" -DFAWKES_BUILD_EXAMPLES=ON \
    -DCMAKE_DISABLE_PRECOMPILE_HEADERS=ON -DCMAKE_CXX_SCAN_FOR_MODULES=OFF
```

下一个点大概会是 middleware，还要先构思一下这部分怎么设计。

刚好我后面请了几天假，这个假期一直到 10/12 号。

---

好了这周就，下周见
