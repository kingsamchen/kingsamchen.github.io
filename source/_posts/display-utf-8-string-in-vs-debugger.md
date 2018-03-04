---
title: 在 VS C++ 的调试器中正确显示 UTF-8 字符串内容
categories: PROGRAMMING
date: 2018-03-04 22:13:31
tags: [Visual Studio, debugger, UTF-8, c++]
---
众所周知，UTF-8 在 Windows 上一直都不是一等公民，在 MSVC 的调试器里，`std::string` 默认按照本地编码解释，在中文系统上是 GBK 或 GB2312。

于是，如果一个 `std::string` 或者 `char[]` 里存储的是 UTF-8 编码的字符串，那么非 ASCII 部分就会乱码。这在调试中是一个非常不好的体验。

研究了一下发现早在 VS 2005，调试器实际上就已经支持了对 UTF-8 字符串的显示，只是不作为默认的编码。

按照 UTF-8 进行解释的方式很简单，在 watch 中添加**字符串起始地址**，后接 `, s8` 即可。

注意的是，对于 `std::string`，要用 `std::string::c_str()` 或者 `std::string::data()` 获取字符串起始地址。

更简单的做法是在 intermedaite window 中，使用 `? str.c_str(), s8`

注：如果变量被优化后提示 `c_str()` 或 `data()` 无法访问到有效数据，则可以使用 `&str[0]`。
