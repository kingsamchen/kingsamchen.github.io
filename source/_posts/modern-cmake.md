---
title: Modern CMake
categories: PROGRAMMING
date: 2018-06-19 15:22:07
tags: [cmake, c++]
---
但凡有点历史的东西，在演进到一个新的阶段时，总会总结一套新的 practices 然后冠之以 modern，例如 modern C++，还有今天的主题 —— modern CMake。

上周花了一点时间稍微研究了一下所谓的 modern cmake，然后将 KBase 在 POSIX 上的 cmake 文件都按照 modern cmake 的做法做了修改，结果可见[此](https://github.com/kingsamchen/KBase/commit/da601fe5afaa54b151e64b3ae86faf515b1cae87)

Modern cmake 有什么好处就不说了，有很多文献可以查，而且我觉得按照目前官方文档的尿性，modern cmake无非是换一种方式踩坑，虽然坑的数量少了不少。

只说几个最核心的点。

首先 modern cmake 推荐按照 module 的方式拆分一个大工程，一个 module 对应一个 CMakeLists.txt，可以是一个 lib，也可以是一个 executable。

每个 module 抽象成一个 target，然后使用新的 `target_*` 属性去定义这个 target。并且每个 target 之间的属性是可以互相独立的。

工程最顶端是一个 CMakeLists.txt，定义一些全局的属性，然后通过 `add_subdirectory()` 来关联所有的 module。

几个自己研究过程中看过的一些资料：

[Effective Modern CMake](https://gist.github.com/mbinna/c61dbb39bca0e4fb7d1f73b0d66a4fd1)

[Using Modern CMake Patterns to Enforce a Good Modular Design - Mathieu Ropert - CppCon 2017](https://github.com/CppCon/CppCon2017/blob/master/Tutorials/Using%20Modern%20CMake%20Patterns%20to%20Enforce%20a%20Good%20Modular%20Design/Using%20Modern%20CMake%20Patterns%20to%20Enforce%20a%20Good%20Modular%20Design%20-%20Mathieu%20Ropert%20-%20CppCon%202017.pdf)

[CMake - Introduction and best practices](https://www.slideshare.net/DanielPfeifer1/cmake-48475415)

[Effective CMake](https://www.slideshare.net/DanielPfeifer1/effective-cmake)

最后，虽然 CMake 看起来已经要成为 C++ 的 de facto build system，但是靠谱的资料却没多少，也缺少像 C++ Core Guideline 这种有影响力的半官方指南。
