---
title: 一周杂记 in Week 1 Mar 2025
categories: CODE-LIFE
date: 2025-03-11 00:58:49
tags: [杂记]
---
本周（03/03 ~ 03/09）是3月份第一周。

## Life

\#1

周二皮蛋做完手术多呆了一天之后我就和医生说准备下午放归了，让医生打一针长效消炎针，防止放归后感染。

皮蛋在航空箱里也一直叫个不停，估计是真的有点怕。到了生活地方后，小蛋凑了上来，但是我一打开航空箱们，皮蛋火箭一样咻的直接冲了出去然后就躲到栅栏后面的草丛里了。。。

过了几天观察皮蛋应该恢复的不错，状态也还可以。

所以这次 TNR 也算是成功了。不过后续就不给他打疫苗了，毕竟反应是真的大。

\#2

最近项目一点好转的趋势都没有，老张反倒越来越癫了，一直一厢情愿的觉得自己没问题都是杭州这边的人不行，执行的有问题。

摊手。

估计今年就得有各种奇葩而且 drama 的事情

\#3

周三公司电影俱乐部看了宽恕，有点难绷，很难评。

周日本来打算去古镇玩玩，但是出发前发现天气不太行，媳妇儿说要不然改看电影吧。

然后就开车去看了《猫猫奇幻漂流》然后看了几十分钟媳妇儿就睡着了 🤡

\#4

- **宽恕 Miséricorde** 2/5 这真的是2024年的电影吗？为什么看完感觉被强行塞了一口1980s的过期发霉的面包的感觉?欲望突破显示理智我可以理解，但是有必要做的这么勉强抽象降智吗？
- **猫猫的奇幻漂流 Straume** 3/5 故事性一般，主线推进的太慢。最后一群一开始失去目标的个体因为猫组在一起倒还算可以

## Work

\#1

**CppCon 2022 | GPU Performance Portability Using Standard C++ with SYCL - Hugh Delaney & Rod Burns** https://www.youtube.com/watch?v=8Cs_uI-O51s&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=91

- SYCL 这种 gpu 框架的介绍

**CppNow 2024 | Fun with Flags - C++ Type-safe Bitwise Operations - Tobias Loew** https://www.youtube.com/watch?v=2dzWasWblRc&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=41

- 作者写的一个 type-safe 的 enum flags 的库，已经 propose 给 boost 了，但是截止目前还没进
- 这个库核心想解决的就是 enum flags 做 & | 的时候希望能把数值操作转换成带类型的行为，这样只要不匹配就能编译期报错；同时提供一些 handy functions
- 不过看起来还是会和 https://clang.llvm.org/docs/analyzer/checkers.html#optin-core-enumcastoutofrange 冲突

**C++ Weekly - Ep 469 - How to Print in C++23** https://www.youtube.com/watch?v=s6CZmNOCoQU

- 核心就一句话：C++ 23 的 library 实现了 `std::println()`
- 现在三家编译器最新版本的 stdlib 都实现了这个

**C++ Weekly - Ep 468 - Use -fvisibility=hidden!** https://www.youtube.com/watch?v=vtz8S10hGuc

- gcc/clang上动态库默认不要导出符号，否则 fPIC 下会导致很多函数调用无法被内联
- 顺带提一嘴，里面有个 `-fno-semantic-interposition` 的参数是让编译器假设导出符号不会被 LD_PRELOAD 这种给动态插桩，让编译器进行激进优化

**C++ Weekly - Ep 385 - The Important Parts of C++20 In Less Than 8 Minutes!** https://www.youtube.com/watch?v=N1gOSgZy7h4

- C++ 20 major features quick guide

**C++ Weekly - Ep 386 - C++23's Lambda Attributes** https://www.youtube.com/watch?v=YlmxNJnone0

- C++ 23 开始可以给 lambda 指定 attributes 了；不同位置的参数应用到返回值，lambda 自身类型， 参数等

**C++ Weekly - Ep 384 - Lambda-Only Programming** https://www.youtube.com/watch?v=z5ndvveb2qM

- 单纯整活的

**Avoid exception throwing in performance-sensitive code** https://lemire.me/blog/2022/05/13/avoid-exception-throwing-in-performance-sensitive-code/

- 其实核心点还是不要拿 exception 做 flow control…

**How to Store an lvalue or an rvalue in the Same Object** https://www.fluentcpp.com/2022/05/16/how-to-store-an-lvalue-or-an-rvalue-in-the-same-object/

- 核心点是拿 std::variant 存 value or reference
- 但是实际上作者不用自己费时间写那个 reference wrapper，直接用 std::reference_wrapper 就好了

    ```cpp
    template<typename... Functions>
    struct overload : Functions... {
        using Functions::operator()...;
        explicit(false) overload(Functions... functions)
            : Functions(functions)... {}
    };

    TEST_CASE("varaint with reference") {
        std::variant<std::string,
                     std::reference_wrapper<std::string>,
                     std::reference_wrapper<const std::string>>
                vars;
        std::string s = "hello";
        vars = std::cref(s);
        std::visit(overload([](std::string& vals) { fmt::print("stored as value = {}", vals); },
                            [](std::reference_wrapper<std::string> refs) {
                                fmt::print("stored as reference = {}", refs.get());
                            },
                            [](std::reference_wrapper<const std::string> refs) {
                                fmt::print("stored as const-reference = {}", refs.get());
                            }),
                   vars);
    }

    ```


**gcc profiler internals** https://trofi.github.io/posts/243-gcc-profiler-internals.html

- gcc PGO 的用法和 pgo 文件格式科普
- 然后是比较深入的讲了一下 pgo 这部分主要做了哪些 counting 和数据处理（这部分没怎么仔细看）
- 最后提了一下对 python 做 pgo 导致 gcc ICE 进而发现 pgo 代码实现有问题

**Assignment for optional<T>** https://brevzin.github.io/c++/2022/05/24/optional-assignment/

- 这篇的 `optional<T>` 是作者自己实现的，并且支持 `optional<T&>` 的东西
- 正片的核心其实在中后半段，`optional<T&>` 的赋值到底意味着什么，因为 C++ 的 reference semantics 在这里把事情搅乱了，到底是 underlying value assignment 还是 reference rebinding
- 实话说，标准库的 `optional<T>` 要支持引用必须要用 `optional<reference_wrapper<T>>` 反而让问题看的清晰

**Branch/cmove and compiler optimizations** https://kristerw.github.io/2022/05/24/branchless/

- cmov 因为有 dependency chains 的问题所以开销也不算低；经验上只有分支预测准确率低于 75% 的时候用 cmov 才有优势；同时现在分支预测的优化能力是很强的，所以很多时候 branchless 反而会让代码变慢
- `r = c ? a : b;` 这种产生的IR和分支是一样的，要 branchless 对编写代码有技巧要求
- 一些预期外的优化会干扰 branchless 指令生成，比如 jump threading；同时 llvm 后端因为倾向大多时候 branch code 性能更好所以优化处理后反而会导致不生成 branchless 指令

\#2

这周因为一些原因突然想起来重新研究一下 httprouter，但是又因为另外一些原因，第二天就停止了。

最近发生了很多很癫狂的事情，具体以后再说吧。最近真的是心累了

---

好了这周就这样，下周见
