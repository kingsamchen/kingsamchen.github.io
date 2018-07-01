---
title: Monthly Read Posts in Jun 2018
categories: PROGRAMMING
date: 2018-07-01 22:45:10
tags: [c++, compile-time, assert, RVO, functional, golang, goroutine, threading model, epoll, select, poll]
---
## Programming Language

[CppCon 2015: Richard Powell “Intro to the C++ Object Model"
](https://www.youtube.com/watch?v=iLiDezv_Frk)

基本覆盖了 POD，无需函数类，单继承的情况，扫盲效果俱佳。

至于为什么没有包含 multiple inheritance / virtual inheritance，作者说自己不是很 condifent with that。

讲道理，MI / VI 在一般的设计里都会尽量避免，而且各种乱七八糟的 case 很多，这里不涉及反而是个正确的做法。

---

[Assertions](https://akrzemi1.wordpress.com/2011/04/06/assertions/)

首先，我很赞同文中作者对于 assertion 是 _guarantees given by the author of a piece of code to himself/herself_ 但是接下来的大部观点都不赞同。

比如作者认为不应该 assert on precodintions & postconditions，因为 assertion 是 for implementation details，这个思路其实不对。

关于 assertion，目前看过最好的阐述还是 [Writing Solid Code](https://book.douban.com/subject/3406939/)，推荐去阅读这个。

---

[Compile-time computations](https://akrzemi1.wordpress.com/2011/05/06/compile-time-computations/)

这篇 post 展示了 constexpr functions 错误报告的常见手段：抛异常。

这个在之前的某个 cppcon talk 里有专门的介绍。

另外，文中更进一步，抽出专门的 constexpr validation functions，并通过 comma operator 串联。

C++ 14 支持多句之后，连 comma operatos 都可以免掉了。

---
<!-- more -->
[C++: On Using int*_t as Overload and Template Parameters](http://ithare.com/c-on-using-int_t-as-overload-and-template-parameters/)

Method 1: Choose use of fundamental types or `int_*` types, and then use it consistently, at least in your sub-project wide.

Method 2: Build a mapping between fundamental types and `int_*` types.

We can change name of a type as long as we preserve its behavior.

---

[CppCon 2015: Jacob Potter “Compile-time contract checking with nn"](https://www.youtube.com/watch?v=mVfL0mQU3Bg)

Dropbox 出品的类似 `gsl::not_null<T>` 的一个设施。

目标也是 compile-time not-null check，但是支持了标准的 `unique_ptr` 和 `shared_ptr`

---

[Make your functions functional](https://www.fluentcpp.com/2016/11/22/make-your-functions-functional/)

前半部分解释了为什么 global variables are bad，然后引出了 functions should be functional。

后半部分是提议大家按照 functional 的方式来实现 functions，即：通过返回值来返回。同时针对 1）性能问题 2）返回多个值 3）错误处理 三种情况提供了 workaround

1. 性能不是问题，因为有 RVO 和 move semantics
2. 使用专有 struct 或者 tuple
3. try `optional`

---

[Return value optimizations](https://www.fluentcpp.com/2016/11/28/return-value-optimizations/)

简单介绍了 C++ 的 RVO 和 NRVO

---

[Heap allocation of local variables](https://arne-mertz.de/2015/01/heap-allocation-of-local-variable/)

Default to use stack allocation.

---

[When the Compiler Bites](http://nullprogram.com/blog/2018/05/01/)

Fun stories about compilers doing things wrong.

## Concurrency

[select / poll / epoll: practical difference for system architects](https://www.ulduzsoft.com/2014/01/select-poll-epoll-practical-difference-for-system-architects)

非常棒三种 IO Multiplexor 的介绍、对比文章。

尤其对每个方法的优缺点以及适用的上下文做了比较详细的介绍。

---
[The Go scheduler](http://morsmachine.dk/go-scheduler)

其实就是当年的 M:N threading model 新瓶装旧酒

## Misc

[Dealing with Legacy Code](http://gulu-dev.com/post/2015-05-09-legacy-code)

标题是我自己改的。

这篇 post 主要谈了如何靠谱地进行重构/重写

两个方法：
1. Managed Rewrite

    本质其实就是 divide-and-conquer，把一个大系统的重构分解成多个互相独立的模块，然后确定先后顺序以及模块的重要程度。

    如果中间发生问题，可以迅速回退到上个一个安全点

2. Parallel Implementation & Adopation

    新老实现一起跑，等到数据表明新系统已经可以替代老系统，就讲所有流量切到新系统，老系统下线。

    感觉比较时候后端服务。

---

[Compiler bug? Linker bug? Windows Kernel bug.](https://randomascii.wordpress.com/2018/02/25/compiler-bug-linker-bug-windows-kernel-bug/)

核心：使用 memory-mapped IO 写 PE 文件后立马通过 `LoadLibrary()` 加载，在磁盘IO非常重的情况下，有概率会出现加载的内存页是空数据（filled by all zeros），因此此时 cache / buffer 还没有刷新。

可以通过使用 `FlushFileBuffer()` 作为 workaround。

不过这篇 post 当作小说看也蛮有意思的

---

[CppCon 2015: Titus Winters "Lessons in Sustainability...”](https://www.youtube.com/watch?v=zW-i9eVGU_k)

Change is inevitable for monolithic codebase, and how to make the codebase sustainable.

- culture of testing
- policies
- techonogies
- practices

---
[Minimalist C Libraries](http://nullprogram.com/blog/2018/06/10/)

如何用 C 写一个小而美的库。

作者的几个观点：
1. 控制接口数量，越多的接口通常代表越高的复杂度
2. 尽量避免库内部进行内存分配操作
3. 量避免库内部发生 I/O 行为。因为 lib 自身通常是 environment agnostic，没准程序自身跑在 green threads 上
4. 控制对外暴露的数据结构的数量

后文提供了三个作者自己写的 lib 作为例子。