---
title: 避免使用 breakpad 时调试模式下某些错误跳过调试器自动引发崩溃处理
categories: PROGRAMMING
date: 2016-11-20 12:30:16
tags: [google-breakpad, invalid paramter, 'operator[]']
---
### 现象

某个小朋友（虽然和我同年同届...）写代码时不注意，出现未知错误，但是即使调试器启用所有断点选项，也并没有断在出现错误的地方，而是直接进入了我写的 crash handler。

### 分析过程

在调用会出现错误的函数前加入

```c++
*static_cast<int*>(0x0) = 0xDEADBEEF;
```

调试运行，debugger 成功断下；说明相关功能均正常，问题应该只和该函数产生的错误有关。

打开 crash handler 生成的 dump 文件，设置符号表，发现错误发生原因是： `vector::operator[]` 访问下标越界，但是该错误直接触发 crash handler

### 解决方案

去掉 client 安装 crash handler 前的

```c++
_CrtSetReportMode(_CRT_ASSERT, 0);
```

即可解决问题。

### 原因

因为标准规定 `vector::operator[]` 操作不做 bounds checking，有问题情况下直接导致 undefined behavior；MSVC 为了更快诊断问题，Debug 模式下会对这个操作进行 CRT ASSERT。

而 `_CrtSetReportMode()` 屏蔽了 CRT ASSERT，所以直接进入了 crash handler。