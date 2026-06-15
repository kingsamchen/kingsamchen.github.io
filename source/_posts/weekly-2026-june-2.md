---
title: 一周杂记 in Week 2 June 2026
categories: CODE-LIFE
date: 2026-06-15 22:48:31
tags: [杂记]
---
本周（6.8 ~ 6.14）是6月份第二周，这周末杭州开始进入梅雨季。

## Life

\#1

周四早上出门前发现秋宝右脚不知道啥时候被虫子咬了，有点发红，但是我又不确定是否严重，所以交代了我妈等老婆下班之后让她检察一下。

于是到了傍晚还没下班的时候媳妇儿发微信给我说秋宝的脚开始红肿了，现在真的变成小猪蹄子了 🤣

好消息是并没有很严重的炎症反应也没有发烧，所以媳妇儿只是下单买了一瓶 炉甘石洗剂 进行涂抹，并且观察一晚

万幸到了第二天早上红肿就有消退的迹象，到了周五傍晚又涂抹了一次之后，周六已经基本全消退了。

\#2

这周六开始杭州正式入梅雨季，未来2-3周估计都是伴随阴雨天气了，难绷啊

\#3

这周公司的 Tour de Zoom 2026 正式开始，我报了跑步这一项，要求是周一到周五五天要跑满至少 21KM。

搁去年这绝对是常规操作，但是今年因为通勤距离变远了 + 时长要早点下班陪猪猪，所以难度会比去年增加一些。

还好最终依靠周五中午52分钟跑了9KM出头，最终达成了目标。

至于有没有礼品到时候再说。

另外这周日拳击课之后顺带又上了教练的核心课，当作心肺放松也还不错，刚好我的核心肌群确实也要做一些加强。

## Work

\#1

**CppCon 2023 | Better CMake: A World Tour of Build Systems - Better C++ Builds - Damien Buhl & Antonio Di Stefano** https://www.youtube.com/watch?v=Sh3uayB9kHs&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=41

- 核心是 tipi 基于 CMake 做的一些 wrapper 的改进，在
- parallel build / reproduciblity(hermetic) / caching / dependency management 上的改进
- tipi 以前我记得是独立的 builder，现在看 roadmap 是要做成 CMake 的 wrapper tool 的样子，有点意思

**C++ Weekly - Ep 536 - Devirtualization and performance with final** https://www.youtube.com/watch?v=MbqWWkP8Ras

- 如果class A 继承自 class B 并且有虚函数，那么如果 A 以后不希望被其他类继承，声明为 final 可以在调用 A 自身函数时做 de-virtualization 优化；类似的如果在A上调用B的函数，编译器也能优化

**C++ Weekly - Ep 130 - C++20's for init-statements** https://www.youtube.com/watch?v=gqhHbhBoDtw

- C++ 20 开始 for-range loop 可以提供一个 init statement
- 看视频真正的核心是可以提供 `for (const auto& f = get_obj(); const auto& e : f.list())` 这种避免出现 UAF

**C++ Weekly - Ep 129 - The One Feature I'd Remove From C++** https://www.youtube.com/watch?v=GeimpPHYYPk

- 省流：吐槽的是 数组在 eval context 被自动 decay 为 pointer 的特性

**C++ Weekly - Ep 128 - C++20's Template Syntax For Lambdas** https://www.youtube.com/watch?v=ixGiE4-1GA8

- templated lambda 在 generic lambda 之外的一种选择

```cpp
auto l = []<typename T>(const T& foo, const T& bar){};
```

**C++ Weekly - Ep 127 - C++20's Designated Initializers** https://www.youtube.com/watch?v=44rs_hX1dxE

- 介绍 designated initializer，有几个点需要注意
- 初始化顺序是强制要按照定义来的，不遵守编译会失败
- 如果某个 member 做了 in-class default init，使用 desgianted initializer 的时候允许跳过这个

**Sanitizers 系列（一）：拆解 AddressSanitizer 的影子内存与插桩架构** https://zhuanlan.zhihu.com/p/2036016613917467682

- asan 用1字节描述8字节的内存状态，这部分维护内存状态的地方叫 shadow memory
- 每个虚拟内存地址都可以映射到这个 shadow memory，而且只需要通过位移就可以完成，所以能保持 asan 不会让程序的性能劣化的太厉害

**Sanitizers 系列（二）：ASan 全量调参｜环境变量详解与报错屏蔽底层机制** https://zhuanlan.zhihu.com/p/2037817316717679338

- 一些实际的使用 asan 参数的例子，例如设定日志文件，supress 文本等

\#2

这周帮 scheduler 把 git hooks 重新做了，clang-tidy 这个也换上了 clang-toolchain 自带的 clang-tidy-diff

scheduler 新 release 的 cutoff 下周一/二切，端午节结束之后的新一周才会部署，所以差不多还有 1+1 周的时间可以做一些小调整

还没想到是先提前调研 ubi-9/alma-9 还是先研究把 sanitizer 给搞上

---

好了这周就这样，下周见
