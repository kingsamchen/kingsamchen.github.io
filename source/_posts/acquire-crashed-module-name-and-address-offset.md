---
title: 在 Windows 上获取崩溃的模块名和模块内地址偏移
categories: PROGRAMMING
date: 2018-02-10 16:35:01
tags: [Windows, crash, breakpad]
---
如果应用程序存在多个模块，那么有很大的可能通过 `SetUnhandledException()` 安装的崩溃处理函数和发生崩溃的地址不在一个模块内，因此直接在 crash handler 里使用当前模块地址是错误的做法。

一个可行的方法是，利用崩溃时的上下文信息 `EXCEPTION_POINTERS`，拿到触发崩溃的地址 EIP/RIP，然后利用 Windows 7 新增的 `GetMappedFileNameW()` 拿到某个地址所在的模块的完整设备路径。

接下来通过简单的字符串处理就可以获得模块名。

剩下的就是模块内地址偏移。

因为 Windows 程序的 DLL 在加载时很有可能因为 *preferred base address* 被占用了导致 DLL 的符号地址需要先经过一次 rebase；再考虑 ASLR 的影响，崩溃时的 EIP/RIP 基本上是不能拿来作为崩溃标识的，因为同一处崩溃的地址可能每次运行都会变。

解决方案是通过 `GetModuleHandleW()` 拿到模块实际的加载地址，然后再和前面的崩溃地址做一次 distance 运算就可以拿到了模块内地址偏移。

这样，name+offset 基本上可以作为 key 用来做崩溃信息的聚合。