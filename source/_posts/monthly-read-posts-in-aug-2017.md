---
title: Monthly Read Posts in Aug 2017
categories: PROGRAMMING
date: 2017-09-02 12:06:15
tags: [tcp, flow control, self-management, smart pointer, c++, shared library, uuid]
---
[How to Feel Progress](http://jkglei.com/progress/)

一篇讲述如何自我管理的 post

核心内容大概就是：
- Making progress in meaningful work.
- Break a large project into several small pieces.

---

[Lightning Talks: Anatomy of A Smart Pointer](https://channel9.msdn.com/Events/CPP/C-PP-Con-2014/Lightning-Talks-Anatomy-of-A-Smart-Pointer)

适合新手扫盲

话说作者其实在 cppcon 2015 也做了一个类似的 talk，但是没有找到视频，ppt 倒是和14年这个 tech talk 的一致

---

[C++ devirtualization in clang](https://channel9.msdn.com/Events/CPP/CppCon-2015/CPPConD03V033)

编译器有时候会根据上下文，自动 inline 某些虚函数调用。

这个 talk 讲的就是 clang 在这方面的一些细节。

不做编译期，对细节并不是很感兴趣。

另外这个小哥应该是个俄罗斯人，口语发音实在是太卧槽了

---

[C++ WAT](https://channel9.msdn.com/Events/CPP/CppCon-2015/CPPConD02V031)

和上面那个 tech talk 一个小哥，口语听起来想死...

这个 tech talk 专门介绍了一些 C++ 里的坑（UB），作为茶余饭后的点心稍微过一下就差不多了。

工作里要是有人这么写代码，直接拖出去打死。

---

[TCP TIME-WAIT 笔记 - 概览](https://blog.tonyseek.com/post/tcp-tw-overview/)
[TCP Flow Control](http://www.brianstorti.com/tcp-flow-control/)

第一篇本身就是某些 post 的笔记，有时间可以读读原文

第二篇非常细致的解释了 flow control

实话说，理论性的教科书看了很多时候还是云里雾里的，最后还是需要实际工程的洗礼；要不怎么说人的认知是螺旋式上升的呢

---

[Make a class thread safe C++](https://mfreiholz.de/posts/make-a-class-thread-safe-cpp/)

这篇文章看起来很长，其实就说了一点：两个原子的函数的复合函数自身并不保证原子......

....好吧，如果比较闲的话可以看看。

---

[Thinking in Parallel](https://blog.baasil.io/thinking-in-parallel-d854e904d821)

核心观点：thinking in parallel is to think about your data as being made up of small fixed-size subset of data that can be dealt with independently.

---

[Shared Libraries: Understanding Dynamic Loading](http://amir.rachum.com/blog/2016/09/17/shared-libraries/)

这篇 post 比较有意思，算是墙裂推荐。

非常工程的叙述如何在 Linux 上使用 shared library，非常好的扫盲科普文。

---

[How to write documentation that's actually useful](https://insights.hpe.com/articles/how-to-write-documentation-thats-actually-useful-1707.html)

前篇论证为什么要写文档；举例使用的 truck factor theory 挺好玩的...

后面解释应该怎么写文档

讲道理，我觉得如何写文档还真是一个和具体事情强相关的活，除非你和 MSDN 一样可以找到专门撰写的外包团队

---

[A Brief History of the UUID](https://segment.com/blog/a-brief-history-of-the-uuid/)

前半部分是 UUID 的考古，作者文笔还不错，可以当作阅读材料...

后半部分提作者根据自己业务实现的一个 KSUID，优点是吸收 timestamp，可以 k-sortable ordered

先 mark 一下，没准哪天需要
