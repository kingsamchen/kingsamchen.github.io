---
title: 一周杂记 in Week 3 Sep 2026
categories: CODE-LIFE
date: 2026-09-21 23:10:07
tags: [杂记]
---
这周（9.14 ~ 9.20）是9月份第三周，这周事情太多了。

## Life

\#1

周一 Ricardo 带来行李箱让我帮忙保管几天，然后就去浙一办理住院准备一个小手术了，周三晚上开车给他送回去了。

周三的时候趁着人员还比较齐整，Kidd 请了公司楼下的 Hot Woods，别说味道还确实可以

周五和周六趁着 suyang 还没出发，一起吃了个饭 + 喝咖啡，然后闲聊，组里最近发生的事情比较多，所以一聊就收不住了 🤣

\#2

最近组里的一个大事是产品组 Head Strong 官宣离职，然后 US/CN 后端都归崔割管

崔割真的是办公室政治大师，不过也和 Strong 自己确实太废了有关。

以后真的是好日子到头咯

\#3

周六下午拳击课，这次花了很多时间练了勾拳，感觉有非常大的进步；组合套路也拉到老长了，就是有点累

现在有点期待下周的拳击课，以及，越来越想搞一个立式的拳击柱玩玩。

周六上午去媳妇儿前东家医院抽血检查肝功能，还好这次血检结果基本都正常，除了甘油三酯还是显著高于阈值 🫠

另外媳妇儿说我吃的瑞舒伐现在医院开不出来了，估计只能后面去药店买了。

周六拳击课下课之后又马不停蹄的带着媳妇儿开车去永旺梦乐城的 Nitori 看床垫，结果直接买了个新床。

媳妇儿说以后都去 Nitori 了，IKEA 品质太烂了

\#4

然后是周日，狗日的调休

主要是因为媳妇儿不休息，我在家扛不住秋宝的折腾 🤔

## Work

\#1

**CppCon 2023 | Advancing cppfront with Modern C++: Refining the Implementation of is, as, and UFCS - Filip Sajdak** https://www.youtube.com/watch?v=nN3CPzioX_A&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=62

- 分享 cppfront/cpp2 怎么实现的 `is`/`as` 这两个 operator

    ```cpp
    x is T
    x is std::integral
    x is 42

    x  as std::string   // construct/convert a new value
    pb as *Derived      // checked runtime downcast
    v  as T             // extract active variant alternative
    a  as T             // extract from type-erased storage
    o  as T             // unwrap an optional
    ```

- 不过我对这俩操作不是很感冒，我觉得他们 overload 的语义太多了，我们真的需要这么甜的东西吗？

**CppNow 2025 | Techniques for Declarative Programming in C++ - Richard Powell** https://www.youtube.com/watch?v=zyz0IUc5po4&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=29

- 这个老哥的演讲感觉有点意思哦
- 分享他自己做的基于 wxWidgets 的一个 declarative 封装 wxUI

**C++ Weekly - Ep 550 - All The Undefined Behavior** https://www.youtube.com/watch?v=FgXBZaiqP4Q

- 新标准文档在末尾列了 core undefined behaviors
- 不过感觉真没必要一个一个看，还是多开开 clang-tidy 和 UBSAN 吧

**C++ Weekly - Ep 81 - Basic Computer Architecture** https://www.youtube.com/watch?v=ee_ITrcmjf0

- 机器上电后，按一个按键/屏幕，发生了什么的简单版

**C++ Weekly - Ep 80 - Intro to AppVeyor** https://www.youtube.com/watch?v=R8OrWVVf5CM

- 曾经的一个给 github 提供 CI 服务的 appveyor

**C++ Weekly - Ep 79 - Intro To Travis CI** https://www.youtube.com/watch?v=3ulKzD2cmSw

- 类似的一个 TravisCI
- 这个服务似乎还在使用，不过应该流量很小了

**C++ Weekly - Ep 78 - Intro to CMake** https://www.youtube.com/watch?v=HPMvU64RUTY

- 最老版本的 cmake introduction

\#2

这周一直忙着在做 httpd -> fawkes 迁移的一些前置工作

主要还是因为前置需要的东西太多了，就算也有AI，也不是立马能弄完的

\#3

fawkes standalone 这次 backport 了 onion model based middleware https://github.com/kingsamchen/fawkes/pull/33

其他需要 backport 的会抽时间一点点移植过来

---

这周就这样，下周见
