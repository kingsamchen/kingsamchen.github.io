---
title: Fix 使用 GDB 调试 Clang 编译的程序时标准库类型始终显示 incomplete type
categories: PROGRAMMING
date: 2017-09-03 17:25:27
tags: [clang, gdb, linux, incomplete type]
---
### 现象描述

系统是 Linux mint 18，其实就是 ubuntu 16.04 LTS，所用的 clang 版本是 apt 默认的 3.8；gdb 使用系统自带。

使用 clang 编译出来的二进制，哪怕开启了 `-g` 生成调试信息，在 gdb 下 `std::string` 始终显示为 *incomplete type*；而 primitive 类型的值可以正确显示

并且通过 apt 安装 lldb，使用 lldb 调试能够看到变量的值。

因此基本可以确定问题发生在 gdb 和 clang 这一对上

### 解决方案

来自路边社的资料：clang 的 linux 版本输出调试信息时，默认去掉了标准库相关的组件信息 （？？？ MMP）

在编译时追加 `-fno-limit-debug-info` 标志，就可以保证输出完整的调试信息

### 安利时刻

为什么使用 clang 编译的原因很简单，ubuntu 16.04 上的 apt 默认的 gcc/g++ 5.4 有 bug，编译我的项目代码一定会出错...对比直接从 ppa test 安装 gcc 6.x/7.x 而言，安装 clang 无疑最省事儿...

另外我发现这个问题的解决方案得益于 [vscode-lldb-debugger](https://github.com/vadimcn/vscode-lldb) 这个扩展。

正是这个扩展在输出里通过 warning 告诉我需要用相关的编译开关输出完整调试信息，这个问题才的可以解决。（从侧面说明程序员还是应该关注一下 warning XD）

最后安利一个 gdb gui 前端：[Nemiver](https://wiki.gnome.org/Apps/Nemiver)

不过现在看起来 linux 上最好的 gdb/lldb 前端应该是 vscode -.-