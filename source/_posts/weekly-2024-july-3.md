---
title: 一周杂记 in Week 3 July 2024
categories: CODE-LIFE
date: 2024-07-23 17:14:40
tags: [杂记]
---
本周（07/15 ~ 07/21）是7月份第三周，依然高温热的要死。

## Life

\#1

小橘情况稳定后就给他广发英雄帖，征集领养。

一开始通过钱阿姨救助群找到一个想领养的兄弟，不过他这几天要出差，月底才能回。

考虑到只有这一个保底不太稳定而且小橘每天百来块的住宿费还是不便宜的，所以还是持 open 态度寻找其他领养人。

过了一两天有公司同事说想领养，而且想去医院先看看。不过比较 Orz 的是他和他家里人一起去了医院看完小橘之后第二天说经过考虑还是不领养了，不知道是不是觉得自己之前没经验担心养不了这么小的奶猫。

这下让我有点危机，于是拜托医院帮忙征集领养，于是过了几分钟医院就说有个小姐姐有意向，很快啊，啪的一下就找上门来了。

小姐姐家里有一大一小两只布偶，而且小布偶也是从小猫开始养的所以应该经验还是不错的，而且人感觉不错。

思来想去就是你了，于是约好个时间就去了医院，和小姐姐碰了个面，让她把小橘带回新家了。

这次小橘救助花了刚好1K，感觉还行吧 🤪

\#2

电影俱乐部看了最近很火的 Trump 的副总统搭档 Vance 自传改编的电影 **乡下人的悲歌 Hillbilly Elegy** 4/5 远超预期，叙事来回切换但是不会显得细碎，郎导的水准在线。两代问题家庭的一地鸡零狗碎，上流社会看不上的粗鲁环境，对主角来说都是不需要逃避也没法逃避的过去，他是那个被拯救过的人。左派媒体不喜欢这个电影很正常，他们忙着关心那些更加宏大的东西。

## Work

\#1

**C++ Templates 2nd | 8. Compile-Time Programming**

- 传统基于特化模板递归的 TMP 实现
- constexpr 的编译期处理
- SFINAE 和 expression SFINAE（利用 decltype() 作为返回类型并且它的参数是 unevaluated context 结合 comma 操作符来玩 trick）
- if constexpr 只需要编译期 boolean 不需要一定是模板上下文

**CppCon 2021 | C++23 Standard Library Preview - Jeff Garland**

- 几个主要的 C++ 23 library features 的介绍，比如 views 的更新，monadic optional 还有 expect，stack trace 这些
- module 的标准化也提了一嘴，不过感觉真正能用可能还要再过好多年？

**CppCon 2021 | Combining Co-Routines and Functions into a Job System - Helmut Hlavacs**

- 藉由 coroutine 来改造 job system 的一个例子
- 感觉讲的一般…

**CppCon 2021 | Dynamically Loaded Libraries Outside the Standard - Zhihao Yuan**

- C++ 的动态链接，包含 delay loading，还有 plugins 模式
- plugin 严格来说是 ODR-violation
- 然后尽量避免 unload dynamic libs，因为 unload 要避免 tls 以及对象析构函数处理的一些冲突，坑多

**CppCon 2021 | Back to Basics: Algorithmic Complexity - Amir Kirsh & Adam Segoli Schubert**

- Big-O 科普
- Big-O 不是实际性能的唯一决定因素，有时候常数大小也很重要，例如对于 nlogn 算法，64-bit 大小范围内，logn 实际上都是 ≤ 64
- 现代设备要考虑 cache locality：有时候两次迭代分别做一个操作会比一次迭代做两个操作要快；sort 一个链表最快的可能是 copy elements into vector then sort the vector then copy the result back

**CppCon 2021 | Warning: std::find() is Broken! - Sean Parent**

- 这个 talk 比较长，但是干货也不少，就是略微有点细碎
- 核心是将函数的 precondition 符合之后后续的操作才有意义，而 std::find() 的问题是有些实现内部是通过 find_if() 来完成的，而 find_if/find 在 C++ 20 中会要求比较的值参数要是 equally comparable，而这个要求需要满足 a == a，而浮点数的 NaN 直接就不满足 NaN
- 所以倒也不是说 std::find 真的有问题，而是从 lang-spec 角度有些 spec 定义的不够严谨导致实际场合出现的情况不一定能满足前置条件

**Throw，然后掉进二进制边界陷阱** https://zhuanlan.zhihu.com/p/367772341

- 对动态库启用 visibility=hidden 然后手动 export (visibility=default) 某几个函数的时候要注意相关的类也要一并导出，否则遇到 clang + libc++ 的时候没有导出的类跨越二进制边界的时候会认为是两个类型
- gcc/libstc++ 会认为 type_info.name 一致的类型是一样的，但是 clang/libc++ 这对配伍换了一个做法
- 最佳建议是不要用动态库，以及二进制边界的不要暴露 C++ 的类型，用 C-ABI 比较稳定

**Meeting C++ online | Satya Das - CIB - ABI stable architecture for a C++ SDK**

- 虚函数不是 ABI 稳定的，所以不能用作跨越二进制边界
- talk 提到一种方法是引入一个可以导出的间接层，这个间接层导出为 C struct，每个member是一个普通的 C 函数，这个C函数有一个参数是具体的class impl，将操作转发到这个 class impl 上。client code 可以在这个间接层上包一层 pimpl 实现
- 整体上感觉是可行的，并且对于 client 要考虑单独SO升级这种可以考虑一下

**How not to implement std::conjunction in C++11** https://www.fluentcpp.com/2021/04/30/how-to-implement-stdconjunction-and-stddisjunction-in-c11/

- 这篇干货不少啊，利用 std::is_same 配合一个额外的辅助 struct 不用递归模板处理来实现 junction
- disjunction 的实现我也想到了可以逆着来。。🤣

**C++ Weekly - Ep 269 - How To Use C++20's constexpr std::vector and std::string** https://www.youtube.com/watch?v=cuFILbHp-RA

- 跟着写了个demo才发现以前的理解有误：目前 constexpr allocation 的要求是必须在离开 constexpr 上下文时释放掉内存，否则 ill-formed，所以这就导致 vector/string 变量自身没法做到 constexpr，即

    ```cpp
    constexpr size_t foo() {
        // But compiled for `const auto v`
        constexpr auto v = std::vector<int>{1,2,3,4}; // v itself is constexpr
        return v.size();
    }

    int main() {
        constexpr auto s = foo();
        return s;
    }

    // <source>:4:20: error: constexpr variable 'v' must be initialized by a constant expression
    //     4 |     constexpr auto v = std::vector<int>{1,2,3,4};
    //       |                    ^   ~~~~~~~~~~~~~~~~~~~~~~~~~
    // <source>:4:20: note: pointer to subobject of heap-allocated object is not a constant expression
    // /opt/compiler-explorer/gcc-13.2.0/lib/gcc/x86_64-linux-gnu/13.2.0/../../../../include/c++/13.2.0/bits/allocator.h:195:31: note: heap allocation performed here
    //   195 |             return static_cast<_Tp*>(::operator new(__n));
    //       |                                      ^
    // 1 error generated.
    // Compiler returned: 1
    ```

- 另外只要保证 transient context，vector 是可以直接在 constexpr 上下文内调用 non-const 函数，只要这个 vector 自身不是 const

    ```cpp
    constexpr std::vector<int> use_vector() {
        std::vector<int> v{1,3,5,7,9};
        return v;
    }

    constexpr std::size_t vec_size() {
        constexpr auto size = use_vector().size();
        return size;
    }

    template<std::size_t Size>
    constexpr auto vec_to_array() {
        const auto vec = use_vector();
        std::array<int, Size> ary{};
        std::copy(std::begin(vec), std::end(vec), std::begin(ary));
        return ary;
    }

    int main() {
        constexpr auto size = vec_size();
        constexpr auto a = vec_to_array<size>();
    }
    ```


**The Little Things: Everyday efficiencies** https://codingnest.com/the-little-things-everyday-efficiencies/

C++ 随手小优化集锦：

- 迭代容器时优先考虑使用 range-based for 因为 micro-performance 更好；vector 目前的 ABI 对于计算 size() 有额外步骤，并且在循环内部调用函数而函数看不到具体实现体时编译器无法保证容器大小是否会被函数改变因而每次都要重新计算长度；对于 range-for 改变容器大小是 UB
- 使用 reserve，这个老生常谈
- 不要使用 std::endl 因为会额外导致 flush；也算是老生常谈

\#2

Linear Algebra and Its Applications 5e | 1. Linear equations in linear algebra 的 Chapter 1.2 Row reduction and Echelon Forms 开始写习题了，这一小节习题又有34道题…

周中没啥时间做，周末两天做了大概一半吧。这题量确实有点大

---

好了这周就这样，下周见
