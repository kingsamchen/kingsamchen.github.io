---
title: 一周杂记 in Week 4 Aug 2023
categories: CODE-LIFE
date: 2023-08-28 22:24:31
tags: [杂记]
---
本周（08/21 ~ 08/27）是8月份的第四周。

## Life

\#1

本周观影：

- 天生杀人狂 7/10 感觉也就那样吧。一些穿插剪辑的手法感觉我上我也行。人性本恶和纯粹杀人倒是挺符合我胃口。
- 训练日 5/5 剧本流畅，反转不生硬。难得看见丹泽尔华盛顿演坏人

周末因为老婆需要弄PPT和学习英语所以本周的家庭影院取消了

\#2

最近越来越感觉上班没有啥动力，基本只想和同事吐槽扯淡。

对我们项目已经不抱什么期望了，食之无味，弃之可惜，也学不到什么东西。就希望能保持现状，不要一下引入过多的用户，再苟一年半，到那时不管有没有 layoff 起码 N 和 RSU 都算占到大头了，公积金啥的多一年算赚一年。

就我们目前这扯淡的架构，用户多一点再遇到一次 failover 能立马都不知道怎么死的。就这样让US高P们继续 happy 吧

## Work

\#1

本周学习

**CppCon 2021 | Lightning Talks: C++ en Tu Idioma - Javier Estrada**

- cppreference 的西班牙语翻译详情

**CppCon 2021 | In-memory and Persistent Representations of C++ - Gabriel Dos Reis**

- IPR In-memory persisetent representations

**Modern CMake for C++ | 12. Creating Your Professional Project**

- 总结 & 回顾。全书完结

另外看了二三十页的 DDIA，不过章节还没看完，这里就不详细记录了

\#2

上周说到给 esl 完成了 install as package 的支持，这周轮到 uuidxx 了。

因为几个坑都踩过了，所以 uuidxx 的 installation support 做起来还是比较顺利的。

一个比较 tricky 的部分是依赖的 md5/sha hash 之前也是作为一个 static lib 由 uuidxx 自己依赖；但是如果要安装的话，这个 hash lib 其实不是很适合一起安装，因为我们实际上不希望这个 hash lib 对外暴露，只希望它作为内部实现。

解决方案是将 hash lib 作为 OBJECT 打包

```cmake
add_library(hash OBJECT)
```

这样的话最终生成的 uuidxx static lib 就会完整包含 hash 的目标文件

完整的 diff 可以看这里：https://github.com/kingsamchen/uuidxx/compare/master...dev-install-as-package

\#3

解决 CMake package installation 之后下一步自然是研究怎么将自己的 lib 通过 vcpkg 安装为依赖。

一个原因是因为很多时候我们不希望，或者说懒得把自己的lib公开发布到 vcpkg registry 上。

vcpkg 通过 overlay-ports 的特性支持这么做；并且虽然在 Windows 上 X64 triplet 会将依赖都编译为动态库（如果不是库自身强制指定 STATIC），我们也可以通过 overlay-triplet 方式进行调整

https://github.com/kingsamchen/Eureka/pull/9 这个 PR 包含了使用 esl uuidxx fmt spdlog 作为依赖的实验例子

后面有时间/心情的话专门开一篇讲 vcpkg 的自定义包

\#4

这周帮助 suyang 解决了 keybase 在 ubuntu 22.04 LTS 上编译的问题。

因为代码写的差的缘故，keybase 目前只能以 C++14 编译，而我们发现 keybase 依赖的 protobuf 依赖的 abseil 始终会认为目标环境是 C++17，从而生成的头文件和目标文件都用的 `std::optional` `std::any` `std::string_view` 等，而不是他自己的 equivalent。

我们确认了自顶向下都指定了 `CMAKE_CXX_STANDARD=14`。

并且因为 keybase 自己魔改了 protobuf 和 abseil 的 CMakeLists.txt，所以 Github 版本无问题并没有什么卵用。

经过一番追踪发现 abseil 在检测目标环境是会尝试编译一段代码，通过 `__cpluscplus` 来确认编译环境，并且通过 CMake 自己的日志可以发现到 abseil 尝试编译执行这段 feature code 时，`-std=c++14` 这个 flag 并不存在。

interesting，看来这就是突破口了

祭出我最擅长的 Google 大法，果然顺利找到相关问题

- https://stackoverflow.com/questions/40877744/cmake-why-doesnt-cmake-cxx-standard-seem-to-work-with-check-cxx-source-compile
- https://cmake.org/cmake/help/latest/policy/CMP0067.html

好巧不巧，keybase 魔改之后的 minimum version 用的 3.5...

解决方法：构建时候顺带指定 `-DCMAKE_REQUIRED_FLAGS="-std=c++14"` 即可解决问题；不过记得要 clean 之前留下的 cache

这个事情告诉我们，CMake 版本能升级就升级，尤其是对于自己可以控制源码的内部 lib 来说

---

好了这周就这样，下周见
