---
title: 一周杂记 in Week 2 Sep 2024
categories: CODE-LIFE
date: 2024-09-16 14:27:54
tags: [杂记]
---
本周（09/09 ~ 09/15）是9月份第二周，09/15 是中秋假期第一天

## Life

\#1

这周因为中秋所以又很蛋疼的有调休，周六上班，日一二放假，硬生生地要搞一个假期，真蛋疼。

周五清早开车送媳妇儿去富阳，中午吃了饭之后就回家了，下午继续去办公室上班了

周六想了一下还是去办公室好了，果不其然中午有同事提议去川遇吃一顿，算是赶上了 🤣

\#2

周三公司电影俱乐部放映《X》，媳妇儿也有点感兴趣，就作为访客一起看了电影。

下回要是有不错的电影又恰好碰到媳妇儿休假的话可以多拉他来参加 😅

本来周四下午送媳妇儿去富阳但是因为太热了，那边的房间空调又坏了所以就改到了周五早上；于是周四晚上去踢球了。

但是因为这次报名的太少，他们找了4-5个外援，我们大多数人完全不认识的那种。

讲道理这次体验挺差的，因为有将近一半的人不是自己同事，我就感觉特别容易受伤；而且因为他们外援自己想要一队，我和chao就变成外援队里的自己人。

然后对面自己同事对抗的时候还真就挺狠的，chao的小腿和膝盖都被撞青了，还好我当时机智，觉得有点危险，直接变成了划水模式，象征性的踢踢。

感觉最近可以先歇几场了，确实有点不太稳妥

\#3

本周观影

- **X** 4/5 比预想的有意思的多，而且还掺杂了一些导演/编剧自己的私货，剪辑手法有点可以。 这片告诫世人长时间无性生活人会变疯魔 🤣
- **从21世纪安全撤离** 3/5 还好没去电影院看....人物太平了，故事也老套，多少年了这风格看多了还胃疼

## Work

\#1

**CppCon 2022 | Contemporary C++ in Action - Daniela Engert** https://www.youtube.com/watch?v=yUIFdL3D0Vk&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=31

- 以一个带网络和视频编解码的 demo 项目为例子来讲解 C++ 20/23 的特性能带来的帮助
- 属于干货比较多的一个分享，不过需要自己去看看 sample code

**CppCon 2022 | Killing C++ Serialization Overhead & Complexity - Eyal Zedaka** https://www.youtube.com/watch?v=G7-GQhCw8eE&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=34

- 介绍的是作者自己写的一个 C++20 的库 zpp 号称 zero-overhead 的 binary serialization
- 核心点是大量的 compile-time tricks（依赖海量 concepts）还有 force inline 来提升性能；利用类似 boost/pfr 的方式来 visit struct member 来模拟 compile-time reflection

**CppCon 2022 | Deciphering C++ Coroutines - A Diagrammatic Coroutine Cheat Sheet - Andreas Weis** https://www.youtube.com/watch?v=J7fYddslH0Q&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=35

- 感觉是看过来讲的最好的一个 coroutine basics 的 talk，从最常用的 syntax 开始把 promise_type return_type awaitable 这些东西给补全了

**CppCon 2022 | Back to Basics: The C++ Core Guidelines - Rainer Grimm** https://www.youtube.com/watch?v=UONLB7wBVSc&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=33

- 从 core guidelines 里挑出一些来讲讲
- 但是感觉这次这个一般

**C++ Weekly - Ep 373 - Design Patterns in "Modern" C++ (2023)** https://www.youtube.com/watch?v=A_MsXney3EU

- 其实就讲了 observer pattern 在 modern c++ 中可以用 signal/slot 这种手法来替代
- 实现上要注意被引用对象的生命周期

**C++ Weekly - Ep 279 - Quick Perf Tip: Use The Right Iterator Comparison** https://www.youtube.com/watch?v=oelQ4uAw2WQ

- 用 `!= end` 来判断迭代器性能能提升 10%
- 然后评论里提到了这可能不是 JB 和 JE 指令的差距，他们两个地下都是一样的 JCC 指令，性能差异应该来源于 CPU speculative execution 一类的微观优化

**Did you know that C++20 made typename more optional?** https://github.com/tip-of-the-week/cpp/blob/master/tips/233.md

- C++ 20 开始在一些明确需要使用 type 的上下文可以省略 dependent name 前置的 typename

**The Worst Best Practices - Jason Turner - [CppNow 2021]** https://www.youtube.com/watch?v=KeI03tv9EKE

- 非技术性 talk，主要是作者这么多年来的一些个人感悟，比如在网上分享文章会遇到一个很 mean 的人和 comments；如何在 leanpub 上自己出版一些书籍

**Creating a task completion source for a C++ coroutine: Producing nothing** https://devblogs.microsoft.com/oldnewthing/20210325-00/?p=105002

- 系列第33篇
- 利用类偏特化给加上类似 `Tastk<void>` 的支持

**Creating a task completion source for a C++ coroutine: Failing to produce a result** https://devblogs.microsoft.com/oldnewthing/20210326-00/?p=105009

- 系列第34篇
- 手搓了一个 union/variant 把 exception_ptr 塞进去了，然后剩下的就是补足要支持 exception 部分的逻辑

**C++ coroutines: The mental model for coroutine promises** https://devblogs.microsoft.com/oldnewthing/20210329-00/?p=105015

- 系列第35篇
- coroutine 的 promise 基础讲解

**C++ coroutines: Basic implementation of a promise type** https://devblogs.microsoft.com/oldnewthing/20210330-00/?p=105019

- 系列第36篇
- coroutine_traits 讲解

\#2

这周把 nlohmann-json serde 一开始规划的大部分的实现都写完了，后面糊一下 README 就可以 make public 了 🤣

过程中在 ubuntu 2404 上用 gcc-13 编译的时候发现编译有告警失败了，仔细研究了一下发现是 gcc-13 引入的新告警 `-Wchange-meaning`，详细介绍可以见[这里](https://developers.redhat.com/articles/2023/06/21/new-c-features-gcc-13#new_and_improved_warnings)

具体来说就是如下代码

```cpp
struct foobar {};

struct test {
    foobar foobar;
};
```

L84 中 `foobar` 从类型变成了变量，就会触发告警

```
<source>:4:12: error: declaration of 'foobar test::foobar' changes meaning of 'foobar' [-Wchanges-meaning]
    4 |     foobar foobar;
      |            ^~~~~~
<source>:4:5: note: used here to mean 'struct foobar'
    4 |     foobar foobar;
      |     ^~~~~~
<source>:1:8: note: declared here
    1 | struct foobar {};
      |        ^~~~~~
Compiler returned: 1
```

细心的人发现了这对使用标准库的 naming style 的人非常不利啊...

解决方案就是类型名和变量名不要重合 😅

\#3

这周给公司某个模块改代码的时候用了基于 CRTP 实现的 chain-of-responsiblity pattern，用 `std::function` 做类型擦除，这样整体上就避免了虚函数和自己手动的动态内存分配。

简化一下，示例代码大概长这样

```cpp
template<typename T>
struct ExternalEventHanlderBase {
    template<typename H, typename... Hs>
    void setNextHandler(H h, Hs... hs) {
        if constexpr (sizeof...(hs) > 0) {
            h.setNextHandler(hs...);
        }

        next_ = h;
    }

    bool operator()(int n) {
        auto ret = static_cast<T*>(this)->impl(n);
        if (ret) {
            return true;
        }

        return next_ ? next_(n) : false;
    }

    std::function<bool(int)> next_;
};

struct ICSEventHandler : ExternalEventHanlderBase<ICSEventHandler> {
    bool impl(int n) {
        std::cout << "ICS Event Handler\n";
        return n < 10;
    }
};

struct TNEFEventHandler : ExternalEventHanlderBase<TNEFEventHandler> {
    bool impl(int n) {
        std::cout << "TNEF Event Handler\n";
        return n < 50;
    }
};

struct HandlerHead : ExternalEventHanlderBase<HandlerHead> {
    bool impl(int) {
        return false;
    }
};

int main() {
    HandlerHead handlers;
    handlers.setNextHandler(ICSEventHandler{}, TNEFEventHandler{});
    handlers(25);
    return 0;
}
```

https://gcc.godbolt.org/z/TYGsGs85z

---

好了这周就这样，下周见
