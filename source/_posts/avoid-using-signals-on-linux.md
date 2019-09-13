---
title: 避免在 Linux 上使用 signals
categories: PROGRAMMING
date: 2019-09-09 20:27:09
tags: [Linux, Unix, signal, signalfd, self-pipe trick]
---

首先承认这个标题乍看之下很像 troll，但真的不是 troll；“避免使用”总的来说更接近 _whenever possible_ 的意思。

另外，这篇 post 面向的主要是偏底层的、直接使用 system calls 或其 runtime wrapper（glibc）的开发者，最典型的比如 C/C++ 开发者。

其他语言的开发者通常因为要么 runtime “屏蔽”了这部分内容（如 Java）；要么 runtime 自身对这部分做了较大的抽象/改造（如 Golang），因此很难对此 post 提到的各种观点/做法产生共鸣。

FYI：自从工作后中文写作能力一直在退化，因此这篇文章如果存在语句不通顺或者用词不当的地方，烦请见谅。

## 0x00 背景回顾：什么是 signal？

signal 源自 Unix，后来成为 POSIX 标准的一部分，现在则被几乎所有的 *nix 系统支持。

signal 本质上是一种通讯机制，用于系统在某个事件（event）发生时通知某个进程（或线程）。
<!-- more -->
典型的 signal 如

- `SIGSEGV` 程序发生段错误
- `SIGINT` 终端控制台中触发了 Ctrl+C
- `SIGCHLD` 某个子进程结束
- ...

注：较完整的 signal 列表可以参见 [signal.7](http://man7.org/linux/man-pages/man7/signal.7.html) 中的 _Signal numbering for standard signals_ 一节。

signal 的信息传递涉及两个步骤：

1. 发送 signal：如果某个 signal 要被发往进程 P，则操作系统会更新进程 P 的执行上下文数据以标识 P 将要接收 signal

2. signal 接收：当操作系统将执行上下文切回进程 P 时，操作系统会检查 P 是否有未被屏蔽的活跃的（unblocked & pending） signal；如果存在则强制进程 P 对其进行处理。
  如果同时存在多个这样的 signal，则通过一套选择机制选出一个 signal；通常先选择 signal # 最小的。

对于某个 signal，进程 P 可以选择收到之后

- 执行默认行为（可能是直接结束程序；也可能是忽略）
- 强制忽略
- 执行先前安装的 signal handler；这是一个由用户提供的自定义函数

## 0x01 signal 的奇妙特性（天坑）

signal 因为其各种神奇的特性，在业界常被冠之以 _"probably one of the worst parts of UNIX APIs"_。

### Signal Handling and Reentrant functions

当一个进程要通过 signal handler 处理某个 signal 时，原有的执行流程会被操作系统打断（interrupted），紧接着执行 signal handler，结束后恢复执行先前被打断的流程。

所以存在一个可能：

1. 执行某 A 函数过程中被打断
2. 执行 signal handler
3. signal handler 中又调用了 A 函数

如果A函数存在非局部的副作用，例如使用了某个全局变量或者静态变量，则两次执行后可能导致这个变量处于一个不一致的状态，例如 `printf()`。

更糟糕的是，如果 A 函数会分配内存，并且在分配内存过程中被打断，那么由于内存分配（如 `malloc()`）的实现可能底层使用了锁，导致 signal handler 中调用 A 函数分配内存时出现死锁。

因此能在 signal handler 内调用的函数必须要具备 **reentrant（可重入）** 这一特性。这一类函数的学名叫 _async signal safe functions_。

Linux 上 signal-safe 的函数列表可见[这里](http://man7.org/linux/man-pages/man7/signal-safety.7.html)；把作用类似的函数合并同类项之后发现其实符合要求的函数并不是很多。

### Pending Signals and Signal Coalescence

当类型为 S 的 signal handler 被执行时，系统会 block 此类型后续的 signal，导致后续的这个类型的 signal 进入 pending 状态，一直等到 signal handler 结束执行才会触发接收。

同时，如果有一个类型为 S 的 signal 处于 pending 状态，那么后续 S 类型的 signal 都会被丢弃，仿佛被合并一般。

这个特性表明：如果存在一个类型为 S 的 pending signal，则**至少有一个** S 类型的 signal 被系统派发给当前进程。

注：哪怕不在执行 signal handler 中，一个 signal 被生成之后也不会立即被进程响应。这个时候 signal 也处于 pending 状态。所以 signal coalescence 可以发生在你毫无准备地时候。

### System Calls Can Be Interrupted

前面说过，如果进程收到一个信号，则系统可以在调度上下文时候强制进程对信号做出处理。

如果进程此时正在执行一些 IO 相关的 syscall，那么这个 syscall 会失败，并返回 `EINTR`。

蛋疼的是，BSD、Solaris、Linux 对于是否自动 restart syscall，哪些 syscall 可以被 restart 的做法是不同的。

所以有了相对更靠谱的 `sigaction()` 以及 `SA_RESTART` 来取代糟糕的 `signal()`。

然而，即使是在 Linux 上，使用 `SA_RESTART` 也不能保证所有的 syscall 能够重启而不返回 `EINTR`，见[Interruption of system calls and library functions by signal handlers](http://man7.org/linux/man-pages/man7/signal.7.html)

但是相比起 `signal()`，只要能使用 `sigaction()` 就应该使用这个 syscall；至少它又[更好的 portability](http://man7.org/linux/man-pages/man2/signal.2.html)

### Multithreading is Not Compatible With Signals

单纯的 signal handling 已经很难了，线程的出现让这个问题变得难上加难。

Linux 上线程出现的很晚，NTPL（aka POSIX Threads） 进入内核也是在 2.6 之后，比起从 Unix 时代就有的 signal 而言，它们俩之间存在至少几十年的代差。

自从引入线程之后，Linux 的 signal 按照接收层级大致分成两类：

1. process-directed，即发送目标是一个 PID。可以通过 `kill()` 触发；目前绝大多数 signal 都是这个类型。
2. thread-directed，即发送目标是一个 thread，可以通过 `pthread_kill()` 人为触发，典型代表是 `SIGSEGV`

对于 process-directed signals，如果进程有多个线程，则系统会选择**任意**一个线程来处理这个 signal，选择的原则是尽可能不阻碍 signal 处理。

所以面对一个系统发过来的 signal，你很可能都不知道下一个时刻哪个线程会被突然拉取处理这个 signal。

另外前面提到 signal handler 中能做的事情有限，约束非常严格。

牵扯到多线程之后，signal handler 的约束列表里可以加上：**绝对不能使用任何 pthread 函数**。

另外，很多原先的 signal 相关的函数可能都会有多线程对应的版本，因为原来的那些函数可能是 non-thread-safe的，在多线程中使用基本是 unspecified behavior。

典型的比如 `sigprocmask()` 就有对应的 `pthread_sigmask()`。



强行在多线程程序里使用 signal 的后果就是，不光要考虑很难的 thread-safety 还要考虑很坑的 signal-safety，这画面实在太美。

## 0x02 IO事件替代法

虽然你可以主动不使用 signal，但是因为这是传达系统事件的机制，你很可能面临着不得不对某些 signal 进行处理的场景，比如通过处理 `SIGINT`，`SIGQUIT`，`SIGTERM` 来实现服务的 graceful shutdown。

一种经典的对 signal 进行处理的方式是将 signal 转换为某个 file descriptor 的 IO 活动事件，并且将这个 fd 关联到某个的 IO multiplexor 上，fd 的 IO handler 不会受到先前提到的 signal handler 的各种约束，处理上和普通的 IO 没有明显的差别。

### signalfd

Linux 自内核 2.6 以来便提供了 `signalfd(2)`，可以允许你创建一个 fd 并且当某个 signal 来时 fd 变为可读。

具体的使用方式可以参考 man 上的[例子](http://man7.org/linux/man-pages/man2/signalfd.2.html)，这里不再重复引用。

这里提几个使用 `signalfd(2)` 需要注意的点。

**# 0** 

要使用 `signalfd(2)` 接管某个 signal，必须首先将这个 signal 屏蔽。

这是因为 `signalfd(2)` 并没有改变现有的 signal 分发机制。如果不屏蔽 signal，那么 signal 依然会被系统派发到进程中。通过屏蔽这个 signal 将其转为 pending 状态，触发 signalfd 的可读事件。

signalfd 被读取后，对应的 signal 从进程的 signal 列表里移除。

**# 1**

传统的单线程模式下，屏蔽使用 `sigprocmask()` 完成。然而在多线程里使用这个函数，使用这个函数只能修改当前线程的 signal mask，同时调用行为也是不确定且不安全的。

此时必须要需要使用 `pthread_sigmask()`。

新创建的线程会自动继承调用线程的 signal mask。

修改 signal mask 后创建的子进程也会继承父进程的 signal mask。因此通常来说，在调用 `exec*()` 之前应该 unblock signal，不管实在单线程还是多线程里。

当然现实世界里其他程序会不会这么做是另外一件事。

**# 2**

`signalfd(2)` 无法承接同步产生的 signal。

这类 signal 的两个典型的代表分别是：`SIGSEGV` 和 `SIGFPE`。

对于这类 signal，需要使用后面的方法来处理。

**# 3**

总的来说，在 Linux 下使用 `signalfd(2)` 是一个较为便捷的方法。

它的最大缺陷大概是只能在 Linux 平台上使用，不具备通用性。

### The self-pipe trick

这是一个有历史的做法。

这个做法的核心可以用两句话来概括：

1. 创建一个匿名管道 `pipe(2)`，将读端和写段都设置为 non-blocking
2. 程序在 signal handler 里利用 `write(2)` 往 pipe 的写端写一个字节；读端绑定到某个 IO multiplexor 上，通过可读事件触发。

实际例子可参见 _The Linux Programming Interface_ 这本书中的这个 [sample](https://github.com/posborne/linux-programming-interface-exercises/blob/master/tlpi-dist/altio/self_pipe.c)。

使用这个方法的好处是，不需要额外屏蔽 signal，而是直接利用了原来的 signal disposition & handling；同时几乎所有 POSIX 平台都支持这种做法。

如果需要管理多个 signal，那么可以：

1. 为每个 signal 使用一套 pipe pair；或者
2. signal handler 中往写端写入激发的 signal number，大概是四个字节；读端读出后 dispatch 到对应的 handler。

另一种类似的扩展方法是，将 anonymous pipe 替换为 unix domain socket，其余的做法基本一致。

这是 Qt 官方文档中使用的[方法](https://doc.qt.io/qt-5/unix-signals.html)。

## 最佳实践总结

这里点一下题目，最佳时间就是：尽可能不用 signal。

包括但不限于：

- 不拿 signal 做 IPC

  请使用 pipe / fifo / unix domain socket 甚至 tcp/udp socket 等更靠谱的方式

- 不要使用基于 signal 的系统函数，比如 `alarm()`，`timer_create()` 等。基本集中于定时器相关的。

  对于这种需求，请使用 poller wait 或者 timerfd。

- 对于 `SIGCHLD`，如果不需要关心子进程何时死亡，那么可以直接将 `SIGCHLD` 的 handler 设置为 `SIG_IGN`，让系统自动 reap 结束的子进程。

- 对于确实需要处理（注意这里说的是处理）的 signal，使用前面说到的 IO 事件替代法，将 signal 转换为 fd 的 IO 活动进行处理。