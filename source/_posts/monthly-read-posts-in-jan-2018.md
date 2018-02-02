---
title: Monthly Read Posts in Jan 2018
categories: PROGRAMMING
date: 2018-02-02 11:13:33
tags: [c++, constexpr, windows, windows thread, allocator, atomics]
---
[C++17 constexpr everything (or as much as the compiler can)](https://solarianprogrammer.com/2017/12/27/cpp-17-constexpr-everything-as-much-as-the-compiler-can/)

名字不明觉厉结果是入门级读物，有点水...

---

[Thread’s Stack](https://blogs.msdn.microsoft.com/satyem/2012/08/13/threads-stack/)

Windows 线程栈内存的布局，包括 guard page 来提示栈溢出的一些细节

---

**下面是一些 Talk**

[allocator Is to Allocation what vector Is to Vexation - Andrei Alexandrescu - CppCon 2015](https://www.youtube.com/watch?v=LIb3L4vKZ7U)

只能说不明觉厉，因为学不到什么有用的东西...

[CLANG C2 for Windows - Jim Radigan - CppCon 2015](https://www.youtube.com/watch?v=TRgWJuQhkQo)

演讲者似乎是这个项目的主导，并且实现了 MSVC 的 coroutine。

期待什么时候 clang/c2 可以作为 MSVC 的默认配置...

另：演讲者长得有点像 house of cards S4 里的 Mark Usher，很 charming

[Applying functional programming in code design - Michał Dominiak - CppCon 2015](https://www.youtube.com/watch?v=-ROXIG4raiA)

很糟糕的一个 talk，难以想象这么有意思的 topic，talk 全程居然没有一行代码的展示....

不过里面提到的 node-based data structures 适合用来实现 functional programming 的一些基础设施，我一开始完全是懵逼的，直到看了 Seven Concurrency in Seven Weeks 里的第一章还是第二章，才意识到核心是什么。可见这个 talk 是多么的不友好。

[C++11, 14, 17 Atomics - the Deep Dive - Michael Wong - CppCon 2015](https://www.youtube.com/watch?v=DS2m7T6NKZQ)

作者是 IBM compiler team 的 director，说话有点意思。

另外这个 talk 的 PPT 值得研究一下，尤其是涉及到 happens-before / synchronized-with .etc 这类同步语义的内容

---

因为一月份快接近年底了，事情比较多，也很杂，所以自己的学习进度明显被拖慢了，希望从2月份开始能够迅速回到正常节奏。