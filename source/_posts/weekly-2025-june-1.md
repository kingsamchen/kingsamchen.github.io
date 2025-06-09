---
title: 一周杂记 in Week 1 June 2025
categories: CODE-LIFE
date: 2025-06-09 14:01:55
tags: [杂记]
---
本周（06/03 ~ 06/08）是六月份第一周，也是休陪产假的第二周。

## Life

\#1

这周老婆转阴了，回到了月子中心照顾宝宝，提供了一些比较微弱的母乳的支持

第二天还是第三天的时候阿姨也转阴了，并且连续三四天都是阴性后，月子中心方面也解除了阿姨的隔离限制

娃这周也成长得很平稳，体重比起出生时候也长了一斤

我和岳母做了分工，一人来这里陪护一天

事情都在往比较好的一方面发展。

\#2

这周抽了点时间在家看了麦叔的杂种

- **杂种 Bastarden** 4/5 第一感觉就是麦叔表现得真好，感觉不出瑕疵。追求出人头地混入上流圈子，最后发现没人啥也不是

## Work

\#1

**CppCon 2022 | A Tour of C++ Recognised User Type Categories - Nina Ranns** https://www.youtube.com/watch?v=pdoUnvTwnr4&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=122

- 这个 talk 理论程度还是比较高的，而且内容很密集
- aggregate types，标准的定义变来变去，C++ 20 又 relax了不少，并且C++ 20 也允许使用 `()` 来**值**初始化 aggregates，designated initialization 还是必须要用 `{}` 但是需要 `std::is_aggregate` 这个 type trait 的场景少了很多
- “standard layout classes are useful for communicating with code written in other languages。这个是用途

    standard layout classes 的对象地址和第一个 non-static member 地址是一致的，和 base class 地址也一致，利用的这些特性

    还有个 common initial sequence 的trick，应该是要搭配 union 一起用

    有点冷门了…

- trivially copyable types，核心就是 no user defined ctor/dtor，并且也没有 default member initializer；想了一下核心场所是 trivially copy 可以直接转换为 memcpy/memset
- trival types，trivially copyable + non-deleted trivial default ctor，其实就是以前常说的 POD

    `std::is_pod` 在 C++20里已经是deprecated了

- literal type，目前主要用于构造各种 literal objects，构造函数一定是 constexpr

    `std::is_literal_type` C++17 被 deprecated C++20 正式 removed

- structural type，C++20 开始引入的概念，non-type template 类型必须要是 structural types。

    常见的 scalar types，lvalue reference types，某些符合条件的 literal types 都是 structural types

- implicit-lifetime types，为了解决 C struct 代码在C++中不变成 UB 而打的补丁…
- 上面除了最后两个标准库都有 type traits 进行判断

**CppCon 2022 | Lightning Talks: -std=c++20 -- Will This C++ Code Compile? - Tulio Leao** https://www.youtube.com/watch?v=87_Ld6CMHAw&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=133

- C++20新引入了关键字 concept, requires .etc 变量名冲突了就赶紧 rename
- 宏定义冲突这个老生常谈了，一般来说不玩骚操作没事
- `std::filesystem::path::u8string()` 返回类型从 C++20开始由 `std::string` 变为 `std::u8string`
- deleted ctor 会让 struct 不再是 aggregate 也就是不能进行 aggregate initialization
- allocator 删掉了一些成员，例如 construct，不过我怀疑几乎没人用这个；C++ 17 有 construct_at & destroy_at
- accumulate 用 OP 的版本，第一个参数不能是 non-const reference，因为内部实现从 `acc = op(acc, *i)` 变成了 `acc = op(std::move(acc), *i)`

**CppCon 2022 | Quantifying Dinosaur Pee - Expressing Probabilities as Floating-Point Values in C++ - John Lakos** https://www.youtube.com/watch?v=emKOZldM22w&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=120

- 内容量还是挺大的，核心是涉及 floating point 的运算都要小心浮点数精度可能出现一些计算误差
- 浮点数太接近0和太接近1的计算都容易出现问题

**CppCon 2022 | Parallelism Safety-Critical Guidelines for C++ - Michael Wong, Andreas Weis, Ilya Burylov** https://www.youtube.com/watch?v=OD2huQx0Gco&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=123

- cpp coreguidelines 和 misra guidelines 对于并发/并行安全的一些条款的解读
- 因为是委员会成员，又是做自动驾驶相关的，所以才会有这样的一个 talk。。。刚好 michael wong 之前是做并行的

**CppCon 2022 | Lightning Talk: Programming is Fun in Cpp! - Pier-Antoine Giguère** https://www.youtube.com/watch?v=F9c1ZuSRdsM&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=134

- 分享作者自己做的 ray tracing toy
- 然后一些别人开源的 impl

**CppCon 2022 | Lightning Talk: Effective APIs in Practice in C++ - Thamara Andrade** https://www.youtube.com/watch?v=YdZLsSDZ_Qc&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=135

- 感觉大部分也是那种老生常谈的点
- naming, strong types, avoid easily swapped params, better intent .etc 这些

**CppCon 2022 | Lightning Talk: The Decade Long Rewind: Lambdas in C++ - Pranay Kumar** https://www.youtube.com/watch?v=xBkDkCgQsAM&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=138

- 回顾了一下 C++ lambda 的发展

**CppCon 2022 | Bringing a Mobile C++ Codebase to the Web - Li Feng** https://www.youtube.com/watch?v=ew_7JtJ1AW4&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=128

- snapchat 接手了 dropbox 不维护的 djini（使用 IDL 完成 C++ 和其他语言的 interop）并且在此之上结合他们自己的 wasm tool 来实现 C++ 和 typescript/javascript 的互操作，同一套代码逻辑跑多个端

**C++ Weekly - Ep 482 - Safely Wrapping C APIs** https://www.youtube.com/watch?v=RtxZaKUtU-c

- 给 fopen/fread/fclose 做了一套 wrapper，不过我个人不是很喜欢这样做，有点太厚了；个人偏好上直接对 `FILE*` 做一个 wrapper 就够了，因为需要这么 low level 的场合一般都是需要做一些 C 相关的底层交互的，封装太厚了一般都是给自己添麻烦
- 另外评论区有提到，直接给 unique_ptr 的 deleter 指定函数指针会导致 unique_ptr 体积变大，影响 inline，所以推荐做法是走 function object

**C++ Weekly - Ep 338 - Analyzing and Improving Build Times** https://www.youtube.com/watch?v=Iybb9wnpF00

- clang 有 `-ftime-trace` 可以把构建的开销转储成一个 JSON 文件，chrome 的 tracing page就能加载看到火焰图
- gcc 目前没有类似的支持
- 除了PCH来避免重复处理 common headers 之外，jumbo file 可能可以考虑一下；另外这个视频里提到用 `std::make_unique` 因为存在模板实例化，所以比起直接 new 反而在构建上有开销

**C++ Weekly - Ep 337 - C23 Features That Affect C++ Programmers** https://www.youtube.com/watch?v=jOFrKN54M5g

- C23 从 C++ 那边抄了一堆 features…
- 不过 `#embed` 这个不知道算是谁抄谁

**C++ Weekly - Ep 336 - C++23's Awesome std::stacktrace Library** https://www.youtube.com/watch?v=9IcxniCxKlQ

- 介绍终于在23加入的 stacktrace 的功能
- 不过看起来这个不太能在 signal handler 里使用？

**C++ Weekly - Ep 335 - Projects That Need Your Help! - Channel News and Updates** https://www.youtube.com/watch?v=Xu1O-44ikso

- 这一期没有技术内容，主要是 Jason 对于未来的一些想法

**The Sad State of Debugging Performance in C++** https://vittorioromeo.info/index/blog/debug_performance_cpp.html

- 游戏产业比较特殊，即使是 debug build 也要求足够 performant
- debug build 下因为编译器不会做优化，反而会导致一些 release 跑得很好的代码在 debug 下跑得更慢
- `std::move(0)` 在 debug 下三大编译器仍然会产生指令；很多函数都不会被 inline 导致明显的函数调用开销

**The Power of Ref-qualifiers** https://accu.org/journals/overload/30/171/fertig/

- 核心就是区分 lvalue & rvalue 避免临时变量返回 dangling reference

    ```cpp
    class Keeper {
      std::vector<int> data{2, 3, 4};
    public:
      ~Keeper() { std::cout << "dtor\n"; }
      auto& items() & { return data; }
      // ④ For rvalues, by value with move
      auto items() && { return std::move(data); }
    };
    ```


**C++23: flat_map, flat_set, et al.** https://www.sandordargo.com/blog/2022/10/05/cpp23-flat_map

- flat_* 系列是 adapter，底层容器可选，默认是 vector
- boost 1.80 已经有抢先体验版

**Boost.Asio C++ Network Programming [ISBN 9781782163268]**

- 还好只浪费了一天多翻完了。最大的问题就是核心内容太浅了，还掺杂了将近一半基础教学。另外一个问题是内容过时的有点厉害：看的 2nd edition 是 2015 年修订的 C++ 11 finalize 都几年了， sample code 没更新；asio 倒是是当时的最新，但是偏偏后来几年里一些核心组件都改了一遍…

\#2

之前看 Jason Turner 的 C++ Weekly 某一期中有 comment 提到用 `std::unique_ptr` 封装 `std::FILE*` 时不要直接用 `std::fclose()` 的函数指针做 deleter，会增加体积。

虽然之前知道 unique_ptr 会利用 compressed pair (基于 EBO) 来优化尺寸，但是之前写 esl/unique_handle 的时候都没想过加一个 static assertion

所以这次看到这个 comment 索性就加上。

于是专门写了一个 PR 确认 esl/unique_handle 不会引入额外的空间开销，代码见 [Make sure unique_handle won't incur extra space overhead](https://github.com/kingsamchen/esl/pull/23)

\#3

之前一直有想给 terminal prompt 里加上当前是否开启全局代理的提示，但是一直没找到时间做。

这次终于有点时间可以研究了一下。

借助 chatgpt，对 byobu/tmux，oh-my-zsh 和 oh-my-posh 都做了支持。后面专门写一篇 post 来展示这个。

LLM 对于这个场景还是很合适的

---

好了这周就这样，下周见
