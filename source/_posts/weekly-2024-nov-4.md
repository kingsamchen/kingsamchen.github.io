---
title: 一周杂记 in Week 4 Nov 2024
categories: CODE-LIFE
date: 2024-12-01 23:10:15
tags: [杂记]
---
本周（11/25 ~ 12/01）是11月的第四周，也是最后一周，周日就是12月了。

## Life

\#1

上周带大小蛋出院放回他们老窝之后就间隔着为了几天，但是周三周四周五连续三天没看见大蛋，有点担心他。

和之前喂养他们的阿姨沟通后她说会问问附近其他的人，紧接着第二天大概周六清早就发微信给我说，大蛋被我们小区一个遛狗的小姐姐带走了，好像是交给她闺蜜领养了。

我也不知道是高兴还是失望，但是当务之急是先能联系上领养的人；如果靠谱的话，一起把小蛋也收了吧，我们可以随一点“嫁妆”

虽然大蛋不见了，但是小蛋的疫苗还是得安排上的。

因为小蛋太能跑了，所以周六和周日上午没蹲到他，周日下午四点多才终于把他缉拿归案，带去医院打了第一针猫三联。

医生说这个年纪的猫，可能不用打满三针，所以我到时候也按照情况看吧。

\#2

和同事约了周五晚上一起哈利波特死亡圣器下。

于是周五下午电驴骑回家，把车开到了公司；因为周五下午了，所以停车位空出来一些，找停车位也顺利了不少。

顺带还能把之前Ricardo传过来的诱捕笼给带回家

大荧幕上看哈利波特是真的爽啊，点个赞

## Work

\#1

**C++ Templates 2nd | 22. Bridging Static and Dynamic Polymorphism**

- type erasure 以及实现一个简化版的 std::function

**C++ Templates 2nd | 23. Metaprogramming**

- C++ 的 metaprogramming 支持，C++ 14 constexpr 可以降低很多难度
- hybrid programming 混合编译期元编程和运行时计算，能提供很多灵活强大的 constructs，例如 tuple, varaint .etc
- 用传统的 recursive template 实现 tmp computation 的一些开销；不过现在基本都用 constexpr 了吧

**CppCon 2022 | Linux Debuginfo Formats - DWARF, ELF, dwo, dwp - What are They All?** - Greg Law https://www.youtube.com/watch?v=l3h7F9za_pc&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=63

- 可执行文件的 debuginfo 串讲，整体比较细碎，作者中间会伴随着演示
- TIL: -g 等价于 -g2；-g3 会写入更多的信息，例如启用的 macros 等
- debuginfod 可以作为 symbol server，但是需要 gdb-10 至少；ubuntu 2204 是可以用的

**CppCon 2022 | Lightning Talk: The Future of C++ - Neil Henderson** https://www.youtube.com/watch?v=QqQA7_8QuwY&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=67

- 纯搞笑的一个 talk 🤣

**CppCon 2022 | Principia Mathematica - The Foundations of Arithmetic in C++ - Lisa Lippincott** https://www.youtube.com/watch?v=0TDBna3PWgY&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=64

- 又是这个讲抽象的姐
- 我听不懂，但我大受震撼

**CppNow 2024 | What We’ve Been Awaiting For? - How to Build a C++ Coroutine Type - Hana Dusíková** https://www.youtube.com/watch?v=78nwm9EP23A&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=24

- libcurl coroutine 改造，以此为契机讲一下 coroutine 的基础，promise_type / suspend flow .etc
- 这个 talk 整体一般，因为对 coroutine 这部分讲的其实不是很清晰

**C++ Weekly - Ep 433 - C++'s First New Floating Point Types in 40 Years!** https://www.youtube.com/watch?v=YM1nbexgGYw

- C++ 23 引入长度确定的 floating point 类型，例如 float64_t
- 同时还引入了 brain floating point 类型，这种是专门为了机器学习由 Google Brain 设计的，所以叫brain float；bfloat16_t 特点是 exp bits 扩展到了8位，范围更大，代价是牺牲 precision https://en.wikipedia.org/wiki/Bfloat16_floating-point_format

**C++ Weekly - Ep 432 - Why constexpr Matters** https://www.youtube.com/watch?v=QZxfyGmpanM

- 如果一个 task 因为需要多个步骤导致 constexpr 应用受阻，那么可以考虑将非常明确的部分拆出来 constexpr/consteval 化，这样最终结果可能就是整个 task 可以做到 compile-time evaluation

**C++ Weekly - Ep 431 - CTAD for NTTP** https://www.youtube.com/watch?v=yPB_btV8epo

- 本质是 non-type template parameter auto deduction，需要 C++ 20 支持

    ```cpp
    #include <array>

    template<std::array a>
    auto get_at() {
        return a[1];
    }

    int main() {
        return get_at<{1,2,3,4,5}>();
    }
    ```


**[MUC++] Matt Godbolt - Cool Compiler Tidbits** https://www.youtube.com/watch?v=fNk7KTeCYvw

- 现代编译器已经很智能了，尽量给足够的上下文信息让编译器来优化
- 并且现代 CPU 上 imul 指令并不慢，没必要手动展开成位移之和；并且 talk 里给了一个例子，把简单的乘法换成手动展开位移和，被编译器直接“优化”为单纯的 imul 指令了 🤡
- O3 比起 O2 会进行 auto-vectorization 的优化，生成 SIMD 指令；不过这个也有好有坏
- 现代 CPU 增加了一些新指令，对一些特定工作有奇效，例如 popcnt 指令来计算 1-bits 数量

**The big STL Algorithms tutorial: reduce operations** https://www.sandordargo.com/blog/2021/10/20/stl-alogorithms-tutorial-part-27-reduce-operations

- 介绍 accumulate / reduce / transform_reduce 三个函数
- reduce 需要注意的是 binary_op 是有额外特殊要求的，并且参数要满足的条件更多，自己看 reference；reduce 不能保证有序性和结合性
- transform_reduce 是先对 range 做 transform 然后在做 reduce，但是两个 op 顺序是反的

**Don’t reopen namespace std** https://quuxplusone.github.io/blog/2021/10/27/dont-reopen-namespace-std/

- 需要为自己的类型给标准库的一些 utility 加特化的时候直接用 `std::` 这种fully qualified 形式，不要打开 namespace std 添加，会有额外的问题，比如 name lookup

\#2

这周把 Eureka 里 learn-asio 的目录也整合到 learn-cxx 里了，这样后面研究 asio/coroutine 都能方便很多。

而且这几天开始研究用 beast 写 http 相关的基础设施，整一个目录里也比较自然一些。

---

好了这周就这样，下周见
