---
title: 实现 subprocess 中踩过的坑
categories: PROGRAMMING
date: 2022-05-03 23:50:30
tags: [clone, fork, 多进程, Linux, cpp]
---

`subprocess` 是我为 Lumper 实现的一个基础类，主要功能是创建子进程运行指定的 executable，同时支持 I/O 重定向和 clone flags。

因为 Lumper 的目的是实现一个类似 Docker 的东西，所以子进程的创建必须要用 `SYS_clone` 才能设置 namespace 进行隔离。许多直接使用 `fork()` 的三方库其实都是不满足要求的；而 Golang 的 `cmd/exec` 好巧不巧的实现上恰好使用了 `SYS_clone`，算是捡了一个大便宜。

刚好我也想试一下实现一个 industrial-grade 的 sub processing 会遇到哪些问题，所以便有了 `base/subprocess`。

注：下面内容不会涉及接口语义设计、组织设计这些高屋建瓴的内容，只会谈谈我在实现过程中遇到的问题和一些 trade-off 的考量。

源码位置：https://github.com/kingsamchen/Eureka/tree/master/Lumper/base

### 0x0 下马威 —— 编译器的 Bug

Bug 的位置见 https://github.com/kingsamchen/Eureka/blob/master/Lumper/base/subprocess.h#L124

如果使用 `options() = default` 来实现默认构造，会出现奇怪且晦涩的编译错误，而且感觉八竿子打不着。

几轮排除法下来后才确信这大概率是一个编译器的 bug；经过一番 Google 也在 Stackoverflow 上找到了相关的[问题](https://stackoverflow.com/questions/53408962/try-to-understand-compiler-error-message-default-member-initializer-required-be)。

总结一下就是：内部类使用 `=default` 作为默认构造实现，且内部类作为函数的默认参数就会触发。

因为 workaround 会引发 clang-tidy 的 complain，所以我主动 supress 了这个 issue。

### 0x1 避免继承不必要的 fd

和 Windows 的设计不同，Linux 上父进程的 fd 默认会被子进程继承。

传统的多进程单线程模型下，这种行为是合理的；但是对于 subprocess 来说，fork/clone 出子进程后我们会直接用 `exec()` 运行对应的 executable 替换掉子进程，这些继承来的 fd 不仅毫无用处，还会导致即使父进程退出，fd 对应的资源也不会被释放。

一个通行的做法是，父进程在创建 fd 时，如果这个 fd 不希望被子进程使用，统一加上 `O_CLOEXEC` flag。

Modern 一些的系统调用在创建 fd 时基本都支持这个标志，因为可以避免 _create-then-fcntl_ 的 race 情况。

对于 IO 重定向：不管是重定向到 pipe 还是重定向到 `/dev/null` 都是通过 `dup2()` 将源 fd 复制到目标 fd 上，例如 `FILENOSTDOUT`，所以源 fd 创建时可以并且应该设置 `O_CLOEXEC` 标志。

同时 `dup2()` 之后的 **fd 不会带上这个标志**，并且我们也希望这个 fd 对 exec 之后的进程同样有效，因此这个行为是符合预期的。

> The two file descriptors do not share file descriptor flags (the      close-on-exec flag).  The close-on-exec flag (`FD_CLOEXEC`; see `fcntl(2)`) for the duplicate descriptor is off.

1. https://man7.org/linux/man-pages/man2/dup.2.html
2. https://man7.org/linux/man-pages/man3/dup.3p.html


### 0x2 子进程发生错误时通知父进程

父进程通过 `clone()` 创建子进程后，还需要做一些准备工作才能调用 `exec()` 替换掉执行的程序

这些准备工作是可能会发生错误的，而一旦发生不可继续的错误，那么子进程只能退出，但是退出前子进程必须要通知父进程自己失败了。

一个常用的方法是父进程在创建子进程前先创建一个 error pipe（创建时别忘了指定 `O_CLOEXEC`），子进程在 `exec()` 前的准备工作中如果出现错误，则退出前往 error pipe 中写入相关错误信息。

一般来说，`errno` 和约定的错误码即可.

如果子进程的准备工作很顺利，那么 `exec()` 调用时，error pipe 的 fd 都会被自动关闭。

父进程读 error pipe 前先关闭自己这一侧的 write fd，这样一来：

- 如果 read pipe 返回 0，则说明子进程工作顺利
- 如果 read pipe 能读出完整的错误信息，则说明子进程出现错误，父进程要进行相应处理。注意，父进程处理前最好等子进程退出干净之后。

如果我们往 pipe 读写不超过 `PIPE_BUF` 的数据，调用可能会被 block，但是基本不会出现 short write 和 short read。

可以做一个兜底处理：

- 写端 best efforts only；反正通知完就退出了
  **WARNING: 子进程这时千万不要打日志！** 原因后面讲。
- 读端如果出现 short read，或者其他错误，因为没法确定子进程是否正常，所以这里只能假定它正常
  因为如果父子进程有其他的通信方式的话后续也能判断子进程异常退出；而如果父子进程不再需要通信，那么父进程最后 wait 子进程时，也能正常给出问题的子进程招魂

1. 子进程写 error pipe https://github.com/kingsamchen/Eureka/blob/master/Lumper/base/subprocess.cpp#L70
2. 父进程读 error pipe https://github.com/kingsamchen/Eureka/blob/master/Lumper/base/subprocess.cpp#L215
3. https://man7.org/linux/man-pages/man7/pipe.7.html#PIPE_BUF

### 0x3 正确的等待子进程退出

父进程等待子进程应该用 `waitpid()`，因为我们有子进程的 pid 值。并且只要 `clone()` 调用成功，就可以确保这个 pid 值是合法的。

`waitpid()` 返回时有必要检查返回值，成功时返回值应等于子进程的 pid。另外可以把调用失败（返回值为 -1）和等待的 pid 对不上子进程当作两种情况处理，后者可以专门容错。

注：这里检查返回值帮我发现了一开始 clone 有个标志位问题。

参数 `wstatus` 必须要设置，因为这个参数可以区分子进程是正常退出还是被 kill 了，并且可以获得相应的 exit code 或者 signal number。

pid 默认值可以用 -1 或者 0，我个人偏好 -1。

### 0x4 选用对的 syscall

Glibc 有一个包装后的 `clone(2)` 原型如下：

```c++
int clone(int (*fn)(void *), void *stack, int flags, void *arg, ...
          /* pid_t *parent_tid, void *tls, pid_t *child_tid */ );
```

但是这个包装后的函数语义和 raw `SYS_clone` 是有区别的，新进程会直接跑 `fn` 这个函数，并且还得很麻烦的提供一块内存充当栈。

因为这个函数其实是给 pthread 这样的线程库创建线程用的，如果我们想要使用类似 `fork()` 的语义，应该使用 `SYS_clone` raw syscall。

```c++
auto pid = static_cast<pid_t>(::syscall(SYS_clone, clone_flags, 0, nullptr, nullptr));
```

因为我们的目的是创建单独进程，所以不需要设置任何共享资源的标志位。当然子进程还是具有 COW 特性的。

https://man7.org/linux/man-pages/man2/clone.2.html

### 0x5 clone flags 默认加上 SIGCHLD

使用 clone 有一个另外的坑是需要保证使用的 `clone_flags` 启用了 `SIGCHLD`。

在 manual 的 _The child termination signal_ 这节提到这么一句：

> If no signal (i.e., zero) is specified, then the parent process is not signaled when the child terminates.

如果没有这个标志位，并且子进程在父进程调用 `waitpid()` 前就退出了，不管正常退出还是异常退出，父进程都不会收到通知，导致后续的 `waitpid()` 会出错，返回 `ECHILD` 抱怨给定 pid 的进程不存在。

### 0x6 fork/clone 之后 exec 之前的虚无状态

在多线程的环境下，fork/clone 之后的进程处于一个非常危险的状态。

子进程会复制父进程调用 fork/clone 的线程，但是其他资源是全量复制的，比如锁。

如果父进程一个线程在 fork/clone 时，另外一个线程刚好上了个锁。那么子进程里也会有这个锁，而且是处于 lock 状态。

如果子进程没注意又去上锁，那么就会出现死锁。子进程 hang 住的话，父进程除非 kill 或者不去收拾，否则一 wait 也会连着 hang 住。

因为内存动态分配释放也会涉及到锁，这意味着子进程在这个状态时甚至不能进行动态分配内存。

不严格来说，这个时候的子进程能做的事情，等价于 signal handler 里能做的事情。

所以 fork/clone 之后，一直到 exec 之前的代码都要非常小心非常注意。不要动态分配资源，不要抛异常，出错了也不要打日志，因为可能实现里会用到锁进行同步。

那么 `base/subprocess` 的 `options` 为啥要提供一个 callback 允许在这个时候执行函数呢？因为 Lumper 需要在执行 `exec()` 前用 `mount()` 挂上新的 `/proc`。

### 0x7 重定向 I/O 到 /dev/null

直接 `::open()` 打开 `/dev/null` 拿到 fd 即可。

别忘了加上 `O_CLOEXEC` 标志位；另外这个函数在 after-fork-before-exec 这段时间进行即可，也不会遇到什么意外。

### 0x8 析构处理

类似 `std::thread`，`base::subprocess` 对象析构时关联的进程可能还在运行，这会导致两个东西的生命周期不一致了。

目前的做法是如果析构时当前还是 `waitable() == true` 的，就直接 terminate。

后续有必要的话可以引入一个选项，只打印错误之类的。

### 0x9 移动语义支持

这里必须要显式实现两个移动操作，原因有两点：1) 状态一致 2) 移动赋值同样设计生命周期问题。

先看第一个。

标准要求：被移动的对象至少要满足能正常析构。

所以如果我们将一个有关联进程的 `subprocess` 对象进行移动，那么操作完成之后，这个对象一定不能是 waitable 的，否则无法安全析构。

而 bitwise move 做不到这点。

更严格一点，如果我们希望被移动后的对象是 default inited 状态，那么我们还得考虑 pid 的值。

另外，如果移动赋值时 lhs 的对象关联的进程还在运行，那么是不能进行移动的。

所以这里两个移动操作都要显式实现。

---

以上就是实现 `base::subprocess` 时遇到的坑。

细数一下还不少，实现起来需要注意的细节也很多。
