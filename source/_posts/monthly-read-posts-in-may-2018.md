---
title: Monthly Read Posts in May 2018
categories: PROGRAMMING
date: 2018-06-01 22:39:00
tags: [面试, 技术选型, linux, hash table, concurrency, lock-free]
---
### Misc

[怎样花两年时间去面试一个人](http://mindhacks.cn/2011/11/04/how-to-interview-a-person-for-two-years/)

当前行业招聘的不靠谱，招揽优秀人员的难度大。

脱颖而出的核心：良好的阅读习惯 + Github 项目

这里说的两年是针对应届生来说的，对于已经工作的人来说，可以当作是虚指。

不过文中提到的，拥抱变化的三个核心点：
1. 触动内心的大象
2. 建立清晰明确的目标
3. 扫清前进道路的障碍

颇有道理。

---

[何判断一个技术(中间件/库/工具)的靠谱程度？](http://gulu-dev.com/post/2014-07-28-tech-evaluation)

如何做技术选型

### System & Architecture

[聊聊Linux IO](https://0xffffff.org/2017/05/01/41-linux-io/)

A brief introduction to Linux I/O stack.

这篇 post 的质量在国内技术博客里算是少有的干货。

另，关于 page cache 和 buffer cache 的最新的内容，可以参考 Robert Love 在 quora 上的一个[回答](http://qr.ae/TUTNCz)

---

[CppCon 2015: John Farrier “Demystifying Floating Point"](https://www.youtube.com/watch?v=k12BJGSc2Nc)

工程实践上使用浮点数（IEEE-754）需要注意的一些坑。

看之前最好翻一下 CSAPP 中关于浮点数 IEEE-754 模型的基础知识
<!-- more -->
### Data Structures & Algorithms

[Hash Collision Probabilities](http://preshing.com/20110504/hash-collision-probabilities/)

[Hash Table Performance Tests](http://preshing.com/20110603/hash-table-performance-tests/)

[Choosing the hash map's capacity](http://pzemtsov.github.io/2015/12/14/choosing-the-hash-maps-capacity.html)

关于同为搜索类数据结构，（一般情况下）hash table 优于 search tree 的原因是：现代计算机体系结构下，前者比后者要更加 cache friendly

### Programming Language

[Simplifying bootstrapping for virtual constructors](http://insanecoding.blogspot.co.uk/2010/07/simplifying-bootstrapping-for-virtual.html)

core idea 1: 使用 template function 实现 factory functioni

core idea 2: 使用一个 construct-interface 去解决 multiple constructors 的情况

---

[CppCon 2015: Stephan T. Lavavej “functional: What's New, And Proper Usage"](https://www.youtube.com/watch?v=zt7ThwVfap0)

关于 `<functional>` 里主要设施的介绍。

大量干货。

---

[CppCon 2015: Marshall Clow “string_view"](https://www.youtube.com/watch?v=H9gAaNRoon4)

`string_view` introduction。

对于什么是 `string_view` 以及为什么要有 `string_view` 解释的比较详细了。

听 talk 的时候还发现了 `string_view::to_string()` 被移除了，转而往 `basic_string` 的构造函数增加通过 sv 创建的路子；talk 里面也解释了为什么。

### Concurrency & Multithreading

[Pthreads并行编程之spin lock与mutex性能对比分析](http://www.parallellabs.com/2010/01/31/pthreads-programming-spin-lock-vs-mutex-performance-analysis/)

文章内容就是标题了。

不过多嘴提一句，用户态实现 spinlock 很容易跑飞，这点也可以见文章的 comments。

另外，mutex 啥的其实不慢，而且比较稳妥，所以 use mutex whenever possible 应该算是正确的做法了。

---

[What’s up with compare_exchange_weak anyway?](https://blogs.msdn.microsoft.com/oldnewthing/20180329-00/?p=98375)

[How do I choose between the strong and weak versions of compare-exchange?](https://blogs.msdn.microsoft.com/oldnewthing/20180330-00/?p=98395)

Why compare-and-exchange instruction may fail, and when should we prefer the weak version over the strong.

---

[CppCon 2015: Pedro Ramalhete “How to make your data structures wait-free for reads"](https://www.youtube.com/watch?v=FtaD0maxwec)

大量干货。

核心大概是阐述 left-right pattern，一种分离读写操作针对优化的同步手法。

为了引出 left-right，前面讲了大量干货铺垫，例如 lock-free, wait-free, starvation-free, reader-writer lock, copy-and-write .etc

基本没听懂细节，只能先标记一下...这个 talk 可以和 _The Art of Multiprocessor Programming_ 一起看(不懂)了

---

[8 Simple Rules for Designing Threaded Applications](https://software.intel.com/en-us/articles/8-simple-rules-for-designing-threaded-applications/)

除了 Rule 2 （_Implement concurrency at highest level possible_） 需要看一下解释指外，其余7条规则只需要看标题就知道主旨了