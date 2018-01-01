---
title: Monthly Read Posts in Dec 2017
categories: PROGRAMMING
date: 2018-01-01 19:58:49
tags: [c++, fiddler, python, future, unittest]
---

C++ 11 Concurrency: [Part 1](https://baptiste-wicht.com/posts/2012/03/cpp11-concurrency-part1-start-threads.html) & [Part 2](https://baptiste-wicht.com/posts/2012/03/cp11-concurrency-tutorial-part-2-protect-shared-data.html) & [Part 3](https://baptiste-wicht.com/posts/2012/04/c11-concurrency-tutorial-advanced-locking-and-condition-variables.html)

本来想作为 C++ Concurrency in Action 的一个补充，但是发现这个 series （迄今）写得实在是太一般了...看看后续有没有改进

---

[使用Fiddler做抓包分析](http://blog.poetries.top/2017/11/04/fiddler/)

除了 auto-responder ，其他部分内容感觉都好一般

---

[高并发性能调试经验分享](https://zhuanlan.zhihu.com/p/21348220)

文章不短，但是核心还是调试那几板斧：
- 尝试重现/增加重现概率
- 脑补可能的 root cause
- 通过实验排除

文章开头说这类问题适合筛应届生，纯属胡说八道。

首先前面说的那几点虽然是很容易回答，毕竟提纲挈领，但是实际解决过这类多线程/高并发的问题的人都知道，很多时候问题都是高度上下文相关的，换一个上下文很多之前的假设推断手段什么的都没效果，毕竟这是一个强经验相关的 domain。指望碰到一个应届生能熟练解决这类问题（停留在打嘴炮阶段不算），还不如指望哪天出现强 AI。

---

[初探 Python 3 的异步 IO 编程](https://www.keakon.net/2015/09/07/%E5%88%9D%E6%8E%A2Python3%E7%9A%84%E5%BC%82%E6%AD%A5IO%E7%BC%96%E7%A8%8B)
[Python并发编程之协程/异步IO](https://www.ziwenxie.site/2016/12/19/python-asyncio/)

口水文，还好是在地铁上看完的。

---

[磁盘I/O那些事](https://tech.meituan.com/about-desk-io.html)

实战那部分写的不错

另外，文章 url 的 desk 目测是 disk 的 typo

---

[How Google Authenticator Works](https://garbagecollected.org/2014/09/14/how-google-authenticator-works/)

看完想自己写一个

---

[A Few Good Types - Neil MacIntosh - CppCon 2015](https://www.youtube.com/watch?v=C4Z3c4Sv52U)

介绍了一波 `array_view` 和 `string_vew`

可惜 `array_view` 没有进标准

---

[All Your Tests Are Terrible - Titus Winters and Hyrum Wright - CppCon 2015](https://www.youtube.com/watch?v=u5senBJUkPc)

又名：单元测试的几条军规

基本覆盖了实现单元测试 case 的一些基本准则。两个演说者很逗，将相声一样

不过我其实更关心部分这个 talk 并没有涉及到，比如涉及 GUI 的交互逻辑怎么做 ut；负责模块上粒度上怎么抽诸如此类..

---

[Futures from Scratch - Arthur O'Dwyer - CppCon 2015](https://channel9.msdn.com/Events/CPP/CppCon-2015/CPPConD04V009)

一些和 `future` 有关实现细节
