---
title: Monthly Read Posts in Apr 2019
categories: PROGRAMMING
date: 2019-05-02 22:57:13
tags: [cpp, noexcept, golang, gc, mutex, syscall]
---

## Programming Languages

[Destructors that throw](https://akrzemi1.wordpress.com/2011/09/21/destructors-that-throw/)

C++ 11之后的 destructor 默认是 `noexcept`，如果有 active exception 逃逸出 dtor 会直接触发 `std::terminate()`，即使外部有 catch handler。可以用 `noexcept(false)` 显式关闭。

因为 stack unwinding 是可以嵌套的，一个精心设计的场合下（见文中例子），可以做到多个 active exception 不在一个层次里，因此也不会触发 double-exception situation。

---

[Who calls std::terminate?](https://akrzemi1.wordpress.com/2011/09/28/who-calls-stdterminate/)

an exception leaves out from main function

an exception leaves out from initial function of a thread

an exception leaves out from dtor (since c++ 11 with noexcept guarantee)
<!-- more -->
an exception was thrown from

- ctor of a global object
- dtor of a global object
- stack unwinding process (two active exception)

---

[Using std::terminate](https://akrzemi1.wordpress.com/2011/10/05/using-stdterminate/)

stack unwinding 用于处理可恢复的错误（catch then handle）；`std::termiante()` 用于不可恢复的处理。

C++ 标准要求，terminate handler shall terminate the execution of the program without returning to the caller；意味着 handler 里完成需要操作后必须要立即退出，例如通过调用 `std::_Exit()`。

terminate handler 里能进行的操作极为有限，因为进入 handler 时所有全局变量都是不可靠的（可能在某个全局对象的构造函数中因为异常出发了 terminate；或者某个全局对象西沟期间），除了标准库提供的 `cin`, `cout`, `cerr`, `clog` 以及对应的 wide 版本。

terminate handler 函数是非线程安全的，如果多个线程都因为某个意外需要调用 handler，函数内部需要做好安全操作。这进一步加大了 handler 可以进行的操作的难度。

---

[Allocation efficiency in high-performance Go services](https://segment.com/blog/allocation-efficiency-in-high-performance-go-services/)

可以用 `go build -gcflags '-m -m' xx.go` 来对某个源文件做对象逃逸分析。

栈对象的资源开销比堆对象要低得多，因此有需要的话尽可能使用栈对象，例如少用指针，不仅可以有效提升对象存放在栈上的概率，还能减轻 GC 压力。

这里真的要吐槽一下 golang library 的好几处地方还能成为性能瓶颈。。。

使用 interface 会导致目标对象的上下文类型信息被 decay，直接导致大量对象通过逃逸分析后存放到堆上。

剩下的就是各种奇奇怪怪的 hack。

讲道理如果你拿 golang 做一些很核心对性能要求高的东西，折腾各种 dirty hack，还不如考虑换 C++ 或者 Rust。

## Concurrency

[Always Use a Lightweight Mutex](https://preshing.com/20111124/always-use-a-lightweight-mutex/)

use lightweight mutex whenever possible.

## Operating System

[Linux 系统调用](http://blog.sae.sina.com.cn/archives/2200)

可以作为简单的科普文。

其实这部分内容 CSAPP 上写的更加细致。

另：最新的系统，无论是 Linux 还是 Windows，基本都应该用的是专有的 `syscall` 指令进入内核态了。

## Data Stroage

[缓存更新的套路](https://coolshell.cn/articles/17416.html)

[A beginner’s guide to Cache synchronization strategies](https://vladmihalcea.com/a-beginners-guide-to-cache-synchronization-strategies/)


## Misc

[细说API – 认证、授权和凭证](https://insights.thoughtworks.cn/api-2/)

核心还是阐述了 authentication & authorization 的基本概念和使用。

技术方案选取反而只有一点点内容