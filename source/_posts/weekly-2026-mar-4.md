---
title: 一周杂记 in Week 4 Mar 2026
categories: CODE-LIFE
date: 2026-03-30 22:15:24
tags: [杂记]
---
本周（03/23 ~ 03/30）是3月份的第四周，虽然3月份很长，但是这周是最后一周了，惊不惊喜，意不意外。

## Life

\#1

这周因为继续帮 chao 打黑工所以强度还是挺大的，连续几天都搞的比较晚，不过 hrv 倒是稳定下来了，虽然甚至还略低于健康范围的区间左侧。

周六本来打算和老婆一起去市中心的友好饭店旋转餐厅吃药代请的自助餐，但是因为周六我运动计划改变并且还要带秋宝出去转一下，所以上午的时候打算还是不去了。

周日晚上和媳妇儿去了古墩路印象城，本来想给秋宝买点新衣服，结果发现印象城的balabala的婴儿衣服太少了，找了一圈没有合适的。

顺带吐槽一下印象城的停车费，一小时五块钱，一家店满88消费才只能抵扣一个小时...这商场人气都这么点了还这么抠，有点离谱了。

\#2

周三公司电影俱乐部活动，去翠苑浙影时代看了河狸计划

- **河狸计划 3.5/5** 适合带小朋友一起看，比较欢乐。飞鲨那部分直接想起了B级片鲨卷风，几个 kings 登场的时候有点火焰杯三强争霸赛的既视感

## Work

\#1

**CppCon 2023 | Thinking Functionally in C++ - Brian Ruth** https://www.youtube.com/watch?v=6-WH9Hnec1M&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=32

- 还是略偏 introduction 性质了

**CppCon 2023 | Monads in Modern C++ - Georgi Koyrushki & Alistair Fisher** https://www.youtube.com/watch?v=kZ8rbhGgtv4&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=33

- bloomberg 的两位来介绍什么是 functor 和 monad
- 这回感觉真听懂了 🤔

**C++ Weekly - Ep 524 - Line Coverage vs Branch Coverage vs Path Coverage** https://www.youtube.com/watch?v=Gr0aI-TPRiQ

- 某个 sharing session 提到现在 gcc 可以做 line coverage path coverage branch coverage 的统计分析
- 不过这 talk 怎么没说咋开启啊

**C++ Weekly - Ep 171 - C++20's Parameter Packs In Captures** https://www.youtube.com/watch?v=UwYYc5dpvqk

- C++ 20 的 lambda 可以capture variadic arguments 了

    ```cpp
    template<typename ...Args>
    void fn(Args... args) {
      auto l = [...my_args = std::move(args)]() {};
    }
    ```


**C++ Weekly - Ep 170 - C++17's `inline` Variables** https://www.youtube.com/watch?v=m7hwL0gHuP4

- `inline static` 可以在类内直接定义/初始化 members 了

**C++ Weekly - Ep 169 - C++20 Aggregates With User Defined Constructors** https://www.youtube.com/watch?v=B2VbVf6uLq4

- C++ 20 严格规定了 aggregates 中定义了构造函数，即使是 `=delete` 也会阻止 aggregate initialization
- 这个主要是堵上了之前  `struct S { S() = delete; };`  还能 `S s{}` 的bug

**C++ Weekly - Ep 168 - Discovering Warnings You Should Be Using** https://www.youtube.com/watch?v=IOo8gTDMFkM

- 用 `/Wall` 和 `/Weverything` 来发现新告警

**Stack Frame Layout On x86-64** https://accu.org/journals/overload/31/173/bendersky/

- 以 amd64-abi 为主，除了增加用于传参的寄存器，这个架构上会在叶子函数的stack layout开头放一个128bytes 的 red zone，并且保证 singal/interrupt 不会去动他，可以减少 rsp 的移动
- 因为调试信息，例如堆栈可以直接从 dwarf debugging info 中获取，可以优化掉 rbp 的栈基指针用法
- Win64 没有 redzone，用的优化是另外的一套

**Everything You Need to Know About std::variant from C++17** https://www.cppstories.com/2018/06/variant/

- 非常 thourough 的一个介绍 std::varaint 文章，从基础使用到工程实践都有

\#2

这周给 scheduler 那边做依赖得 conanization 进度颇快，不过也踩了一些坑，还好一开始选择策略正确，选择一批包一批包得实验。

找 build team 的说要导新包到 vendors，对面一听我要新增7个包，当即给我科普，说我可以走 jfrog proxy 的模式…

我一听感情这好啊，更灵活，而且可以直接分离 recipe不说，甚至可以直接拿 conan recipe 去下载就好了，更省事了。

不过 curl 这个包比较神奇，release 页面的 assets 和 tags 页面的同一个版本对不上，build team 的给我专门处理了

于是就重新修改了驱动的 build.sh，因为要引入一个走 jfrog proxy 得 deps_list.txt

不过之前的 checkout_list.txt 还是得保留，因为某几个库是公司内部得 repo，只能走提前 checkout 这条路子；不过随着对 conan 更加熟悉之后我发现其实可以不用把 conanfile.py 污染到 checkout repo 得分支里，直接调整 recipe 的 source 策略即可，又卫生又高效

\#3

另外周日晚上想到一个方案，可以让我们的项目用上 pkgconf 并且不会因为之前测试时发现的每个模块的 Makefile 重复调用 pkgconf query 导致的极大耗时。

目前 PoC 验证下来没啥毛病，等我再搞一段时间，最后确认OK了，再 finalize 分享一下。

---

好了这周就这样，下周见
