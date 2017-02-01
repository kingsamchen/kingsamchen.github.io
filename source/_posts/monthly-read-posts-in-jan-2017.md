---
title: Monthly Read Posts in Jan 2017
categories: PROGRAMMING
date: 2017-02-01 23:43:32
tags: [monthly read posts, linker, c++, std::unique_ptr, HTTP, android]
---
[Library order in static linking](http://eli.thegreenplace.net/2013/07/09/library-order-in-static-linking/)

An object file both provides (exports) external symbols to other objects and libraries, and expects (imports) symbols from other objects and libraries.

During linking, the linker maintains a symbol table, which keeps two important lists:
- a list of symbols exported by all the objects and libraries encountered so far
- a list of undefined symbols that objects and libraries demanded to import and were not found yet.

**Case 1: the linker encouters a new object file**

linker first checks symbols the object file exports, and:
1. add them to the list of exported symbols
2. remove them from undefined symbols, if necessary.

Note that, if any symbol has already been in the exported list, then we get a multiple definition error.

Linker then checks symbols the file imports, and added them to the list of undefined symbols, unless they can be found in the exported symbol list.

we can see that for linking pure object files, link order doesn't matter.

**Case 2: the linker encounters a new library**

Linker goes over all the object files in the library, and for each one, it first looks at the exported symbols:
1. If any of the exported symbols are on the undefined list, then this object file is chosen, being added to the link, and is treated as normal object file as above(its exported symbols and imported symbols are processed as normal object file).
2. If any of the object files in the library has been included in the link, the library is rescanned again(because a lately added object file may require symbols exported from one early examined-but-skipped object file).

when linking is done, if any symbols remain in the undefined list, the linker will throw an undefined reference error.

Note that after the linker has looked at a library, it won't look at it again. Even if it exports symbols that may be needed by some later library.

An very important corollary: If object or library AA needs a symbol from library BB, then AA should come before library BB in the command-line invocation of the linker.

**Use Flags to Solve Circular Library Dependency**

Use flags such as `--start-group` and `--end-group` to repeatedly scan libraries, until no new import symbol was found.

But may take significant time to finish linking.

---

[Beginners' guide to linkers](http://www.lurklurk.org/linkers/linkers.html)

和前一篇着重分析链接顺序不同，这篇（其实更像是一个短 paper）介绍了整个链接的基础知识，适合扫盲。

但是如果有一定基础的话，这篇反而没有什么让人觉得惊奇的点。

---

[Exploring std::unique_ptr](http://shaharmike.com/cpp/unique-ptr/)

同样是一篇基础扫盲 posts，适用于检查自己是否对 `std::unique_ptr` 的必备知识存在缺漏的情况。

如果认真阅读过 Scott Meyers 的 [Effective Modern C++](https://book.douban.com/subject/25923597/)，那么 post 里提到的，诸如“为什么使用 `std::make_unique()` 可以带来异常安全” 这种高级议题就变得显而易见了。

另外 post 对 custom deleter 的叙述的反而不够详细。

---

[HTTP Made Easy](http://www.jmarshall.com/easy/http/)

非常赞的 HTTP 协议扫盲文章

这篇 post 从 HTTP 1.0 开始讲起，然后过渡到 HTTP 1.1，所以对于 HTTP 1.1 为什么要求 request header 一定要带 `Host` header 提供了一个非常直观的解释。

此外 post 还增加了 chunked transfer encoding 部分，印象中 Top-down approach 那本 computer networking 是没有提及这部分内容的。

---

[C++ 11 之美](http://www.csdn.net/article/2015-12-03/2826381)

这篇 post 主要有三个点

- 编译期的类成员存在性检查 & 利用 SFINAE 做静态的分支选择（我的[这篇 post](https://kingsamchen.github.io/2017/01/30/check-if-has-member-at-compile-time-in-cpp/) 里的实现就是从这篇 post 改进而来）
- 利用 `std::function<>` 实现 lazy-computation
- 演示了如何将字符串 `"hello/test/20"` 转换为函数调用 `hello("test", 20)`

---

[Android 三大图片缓存原理、特性对比](http://www.trinea.cn/android/android-image-cache-compare/)

讲道理这篇文章很水....

一开始 mark 这篇文章是因为当时还在做安卓直播姬，转眼9个月过去了....

现在为了借鉴文中几个库的架构设计从 pocket 的回收站里挖出来看了一遍，不过想想也就那么回事了....