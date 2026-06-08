---
title: 一周杂记 in Week 1 June 2026
categories: CODE-LIFE
date: 2026-06-08 22:48:42
tags: [杂记]
---
本周（6.1 ~ 6.7）是6月份第一周，Q2的最后一个月。

## Life

\#1

周五 WFH 的时候陪秋宝在我书房玩，让她站在我的 Herman Miller Cosm 上转着椅子玩，结果没想到玩太 high 了有一圈秋宝直接往另外一个方向拱，结果直接从椅子上甩了出去，脸擦到椅子边缘划了一个小伤口，然后顿时哭的天崩地裂。

赶紧抱起来检查一下还有没有哪里受伤，然后我妈开始哄秋宝，顺带狂骂我。

万幸没有其他受伤，就小脸一个小伤口不过没有流血，但是估计也要几天好；和媳妇儿确认不用额外处理后拿碘伏稍微做了一个消毒。

后面媳妇儿用秋宝指甲没剪自己把脸抓了糊弄岳父岳母，还是被BB了十几分钟。

哎，吓死了，以后再也不敢和秋宝玩这个了 🫠

结果周日这娃自己在客厅可劲的爬，然后一个打滑趔趄，直接额头磕到主卧的大门，又开始哭的天崩地裂 🤣

\#2

这周包子教练培训回来，又继续上了拳击课。不过这周因为勾拳姿势生疏了，我和 Hogan 又花了不少时间重新复习/练习勾拳，所以这周其实整体训练量不如上节课。

另外这周上了第一节体态，那个肌肉松解快给我疼死了 🤔

\#3

这周媳妇儿正是提了离职，last day 是6/30，也就是这个月还是要做满。

然后7-8月就开始放暑假了，接着9月份就要开始读博了

老婆说不管是科室同事还是行政的，听说她是去邵逸夫读博，都恭喜她而且离职流程也没卡。

## Work

\#1

**CppNow 2025 | Using TLA+ to Fix a Very Difficult glibc Bug - Malte Skarupke** https://www.youtube.com/watch?v=Brgfp7_OP2c&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=40

- 作者锲而不舍地修了 glibc 一个 condvar bug 好几年，但是并发 bug 很难 reasoning，所以作者学了一下 paxos 发明者的 TLA+，用这个来证明原来的代码 bug 在哪里，以及自己的 patch 如何修复了问题

**CppCon 2023 | Building Effective Embedded Systems in C++: Architectural Best Practices - Gili Kamma** https://www.youtube.com/watch?v=xv7jf2jQezI&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=39

- 主要针对嵌入式，不过我觉得 分层 和 分离网络 的设计是可以被广泛借鉴的
- logging 和 monitoring 这个和 domain 关联性比较大
- simulator 这个有点意思，有点极限测试的意思了

**CppCon 2023 | Expressing Implementation Sameness and Similarity - Polymorphism in Modern C++ - Daisy Hollman** https://www.youtube.com/watch?v=Fhw43xofyfo&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=40

- 软件工程设计中的 saness（interface / implementation）
- 下面是演讲中作为点解的 techs

| Mechanism | Good for | Main cost |
| --- | --- | --- |
| Virtual inheritance | Runtime substitutability, stable interfaces | Runtime dispatch, coupling, object model constraints |
| Templates | Static reuse, zero-overhead generic algorithms | Compile time, diagnostics, possible code bloat |
| Concepts | Explicit generic requirements | Requires careful semantic design |
| Mixins | Reusable behavior units | Hidden coupling, complex composition |
| CRTP | Static polymorphism with shared implementation | Mechanistic complexity |
| Deducing `this` | Reducing member-function duplication, cleaner static patterns | Newer feature; compiler/tooling adoption considerations |

**CppCon 2023 | Lightning Talk: Writing a Better std::move - Jonathan Müller** https://www.youtube.com/watch?v=hvnl6T2MnUk&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=43

- 内容密度比较大，讲的是如何改进现有的 `std::move()`
- 首先解释一下 id expression，可以理解成只有变量，例如 `move(var)` 是允许的，但是 `move(var.foo)` 就不允许
    
    ```cpp
    #define is_id_expression(...) is_id_expression_impl(#__VA_ARGS__)
    
    constexpr bool is_id_expression(char const* const expr)
    {
        for (auto str = expr; *str; ++str)
            if (!std::isalpha(*str) && !std::isdigit(*str)
                && *str != '_' && *str != ':')
                return false;
        return true;
    }
    ```
    
- 更好的 `std::move()` 应该是：owning is moved, non-owning is error, argument must be id expr，non-owning 的意思是拒绝 const qualified 和 lvalue-reference
    
    ```cpp
    template <typename DecltypeT, typename T>
    constexpr std::remove_reference_t<T>&& move_impl(T&& obj)
    {
        static_assert(!std::is_const_v<std::remove_reference_t<T>>);
        static_assert(!std::is_lvalue_reference_v<DecltypeT>);
        return static_cast<std::remove_reference_t<T>&&>(obj);
    }
    
    #define move(...) move_impl<decltype(__VA_ARGS__)>(__VA_ARGS__)
    ```
    
- `move_if_owned()` 的行为是 owning is moved, non-owning is copied, argument is id-expr
    
    ```cpp
    template <typename DecltypeT, typename T>
    constexpr std::remove_reference_t<T>&& move_if_owned_impl(T&& obj)
    {
        // DecltypeT is U   -> U&& (move)
        // DecltypeT is U&  -> U&  (no move)
        // DecltypeT is U&& -> U&& (move)
        return static_cast<DecltypeT&&>(obj);
    }
    
    #define move_if_owned(...) \
        move_if_owned_impl<decltype(__VA_ARGS__)>(__VA_ARGS__)
    ```
    
- `move_always()` 就是在标准的基础上禁止 const 的 move
    
    ```cpp
    template <typename T>
    constexpr std::remove_reference_t<T>&& move_always(T&& obj)
    {
        static_assert(!std::is_const_v<std::remove_reference_t<T>>);
        return static_cast<std::remove_reference_t<T>&&>(obj);
    }
    ```
    

**C++ Weekly - Ep 535 - Getting The Best AI Generated Code** https://www.youtube.com/watch?v=NtbQ-mF3iJE

- 展示如何指导 claude code 写优秀的代码
- 核心：don’t tell, just show how

**C++ Weekly - Ep 134 - The Best Possible Way To Create A Visitor?** https://www.youtube.com/watch?v=EsUmnLgz8QY

- 其实就是使用 overloaded idiom 那个方案

**C++ Weekly - Ep 133 - What Exactly IS A Lambda Anyhow?** https://www.youtube.com/watch?v=br4tez2G9eM

- 解释 lambda 就是编译器生成的一个 callable object；还顺带介绍了 cppinsight
- 注意这是7年多前的视频，不过考虑到 cpp11 应该2015~2016就应该非常普遍也确实有点老

**C++ Weekly - Ep 132 - Lambdas In Fold Expressions** https://www.youtube.com/watch?v=QhY7Fx-YsGs

构造一个 capture lambda 然后对每个 arg 做一些基本变换

```cpp
template<typename... T>
auto do_stuff(T... t) {
  return (t { return t + 1; }() + ... );
}

do_stuff(1,2,3,4,5);
```

**C++ Weekly - Ep 131 - Literals in ARM Assembly** https://www.youtube.com/watch?v=eMluWRp5hbI

- ARM ISA 下超过256的立即数会需要通过 rotate shift + 其他立即数来表示
- 基本就是这么个情况

**Why am I getting an unhandled exception from my C++ function that catches all exceptions?** https://devblogs.microsoft.com/oldnewthing/20230223-00/?p=107867

- 省流：catch block 中再抛出的异常当前的 catch block 是没法捕捉的
- 要么再做一层，要么利用 function try block
- 按照之前的使用经验，如果要显示捕捉异常，我觉得核心路径几个点都要好好设计一下

**Named Booleans prevent C++ bugs and save you time** https://raymii.org/s/blog/Named_Booleans_prevent_bugs.html

- 核心观点就是，if 里不要写复杂的嵌套，把每个条件都弄成一个 boolean 来表示，这样不容易出错
- 但是这样做其实会丢失 short circuits 功能
- 所以看情况使用吧

\#2

这周最大的喜事就是搞定了用公司 CI Jenkins + scheduler 新构建系统构建 docker image。

CI Jenkins 安装 conan packages 这个是一波三折。

一开始 US 的同事说应该可以通过 mount CA cert 来访问那些包，因为 Java 和 Golang 都是这么搞得，但是我自己试验下来发现不行。

于是跑去问 build team，他们说不管我们的 docker-in-docker 方案，我们要拉 conan packages 必须要在 CI Jenkins 自己跑的 docker container 里拉取，因为 conan auth 只会在这一层预置。然后给我科普其他组是怎么用 conan recipe 可以把包装到指定地点 blahblah，以及 diss 我让我不要用 conan official conan recpies...

我觉得他们的方案太垃圾了，但是既然他们限定了只能在外层 docker 跑 conan install，只能研究一下 conan 有没有现成方案修改 install cache storage 的路径。

还好现在有了 AI 这个事情容易很多：直接改写 `core.cache:storage_path` 就行。

但是 conan 不提供 config 直接设置，所以比较奇葩只能用一段 shell 去写 global.conf，不过还好这个也可以让 AI 自己完成。

于是改了一下给 CI Jenkins 用的 `build.sh`，在外层执行 `conan install` 但是 package cache 直接装到项目的 .ci 子目录，然后二阶段在内层 docker 容器里直接用这部分的包；因为路径前后保持一致，所以 CMake modules 可以顺利安装。

后面只需要把几个 jenkins job 的 dockerfile 都调整一下就行。

乐观估计下周可以让 OPS 操作一下把 dev/qa 的切到新构建出包了

\#3

这周遇到的一个小 quiz

`std::tie()` 和 `std::forward_as_tuple()` 有何区别？

区别是，前者创建的是 lvalue reference，而后者创建的是 forward reference

```cpp
template< class... Types >
std::tuple<Types&...> tie( Types&... args ) noexcept;

template< class... Types >
std::tuple<Types&&...> forward_as_tuple( Types&&... args ) noexcept;
```

---

好了这种就这样，下周见
