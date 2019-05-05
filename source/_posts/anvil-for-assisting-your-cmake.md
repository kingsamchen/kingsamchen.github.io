---
title: Anvil -- An Assistant For You CMake
categories: CODE-LIFE
date: 2019-05-04 21:12:05
tags: [anvil, cmake, cpp, project management]
---
### 0x00

前段时间专门抽空做了一个小工具，也就是这里要讲的主题：[anvil](https://github.com/kingsamchen/anvil)

一开始做 anvil 的动力很简单：某次尝试体验一下 Linux SignalFD 功能时想直接使用 ezio 的 `EventLoop` 作为基础事件循环，同时项目使用 cmake 管理。

为了省事，我直接从 ezio 的项目里抠出来 `CMakeLists.txt` 和几个自己写的 `.cmake` 文件，就地[修改](https://github.com/kingsamchen/Eureka/tree/master/UsingSignalfd)。

但事实证明哪怕这样，改动量也不小，原因大体是因为：cmake 里（非函数内定义的）变量作用域是全局的，通过 fetch-content 功能引入的依赖在 `add_subdirectory()` 后的模块里也能看到上一层定义的变量，因此为了防止一些控制型变量发生冲突，我都在前面加上了对应的模块前缀。

所以我面对的就是一大坨变量名的更换，以及少部分声明/属性的调整。

考虑到大部分的文件内容都是可以模板化的，而手动“实例化”不仅费事还很容易出错，所以我就很自然地萌生了写一个工具自动化这个过程的想法。

### 0x01

在经过一两天的短暂思考后，我大致确定了这个工具的定位和需要实现的基本目标，总结起来有三个核心点：

1. 辅助 cmake 而不是试图替代它或深度封装
2. 内建一个轻量型的依赖管理功能
3. 配置化的生成 & 构建流程

首先，第一点是重中之重。
<!-- more -->
现在市面上有很多工具的目标是 cmake 的取代者，或做一个比 cmake 更先进的构建系统。然而除非他们只是想撑起公司内部某个项目而不是试图统一整个社区（比如 Google 的 gn），否则大概率不会成功。

cmake 已经是 C++ 的 de facto build system，并且在社区内的接受程度越来越高：

- Visual Studio， CLion 等商业 IDE 都对 cmake 提供了良好支持
- MS，Google，Facebook 在 github 上的 C++ 开源仓库相当一部分都提供了 cmake 支持，基本上为社区做了表率
- 一些流行三方库也会优先考虑提供 cmake 支持

同时 cmake 近年来也开始慢慢上道，通过提倡 modern cmake 来规范并给出了 cmake 的最佳实践。

因此，即使 cmake 先天缺陷较多，学习曲线陡峭，各种乱七八糟的毛病一堆，但是，只要没有出现一个更好更易用且被多家巨头背书的挑战者，cmake 的地位只为越来越稳。毕竟大势不可逆。

Rant 0：这里吐槽一下 boost 至今还没有官方的 cmake 支持

Rant 1：事实就是，工具往往最后还是要看爹和支持者，俗称：生态。否则 golang 这种设计开倒车的玩意儿这几年也不会增长迅猛。

所以，anvil 的定位就是 cmake 的辅助工具，根据配置生成一些模板化的 cmake 文件，可以让用户快速进入开发。

---

第二点很简单，C++ 的依赖管理一直都是一个老大难的问题。

幸运的是，随着 cmake 3.11 引入 fetch-content 功能模块，中小规模的工程依赖可以很轻易的被解决了。

具体做法可以参考我之前发的一篇文章：[C++ 工程依赖管理新方向：CMake & Git](https://kingsamchen.github.io/2019/02/10/use-cmake-and-git-as-your-cpp-dependency-manager/)

在开发 anvil 的过程中，我把原来获取依赖的操作流程封装成了一个 cmake 模块函数，进一步简化了使用过程。

现在对于使用者来说，只需要在需要依赖的 `CMakeLists.txt` 里加入：

```cmake
declare_dep_module(Catch2
  VERSION         2.5.0
  GIT_REPOSITORY  https://github.com/catchorg/Catch2.git
  GIT_TAG         v2.5.0
)
```

就可以在构建的时候自动拉取 `v2.5.0` 版本的 Catch2。

并且内部会默认复用同一个依赖的源码，避免对于不同的 generator（比如 makefile 和 ninja）需要各自拉一份依赖源代码。

使用 fetch-content module 的另一个好处是，依赖是通过 `add_subdirectory()` 引入的，等同于依赖也是 root project 的一部分，因此依赖的构建参数可以直接由最顶层执行 cmake 命令时指定。

---

因为开发环境的不同，Windows 上如果使用 Visual Studio 作为 generator 则 `cmake -G` 只有一种路数，但是编译时可以通过 `--config` 参数指定编译 Debug 还是 Release 版本；而 Linux 上，Debug 和 Release 则是在生成构建文件时确定。

再加上，工程自身时常会提供 unittest，examples 等附属工程，这些工程的编译通常也是可选的，因此也存在一个编译开关的问题。

之前的做法是针对每个项目写一个 `gen.py`，繁琐不说，可扩展性还特别差。

受到”机制与策略分离“的哲学影响，在几次尝试之后，我最终选择提供一个专门的配置文件用来描述某个工程的构建/编译的参数细节；而 anvil 则用来将这些参数”装填“到具体的构建/编译流程中。

这个配置文件我称之为 configuration set，它包含多个 configuration；每个 configuration 描述了工程的 generation target 和 build mode。

举例：

```toml
[msvc-2019]
    generator = "Visual Studio 16 2019"

    [[msvc-2019.gen]]
        target = ""
        out_dir = "build/msvc-2019"
        args = ""

    [[msvc-2019.build]]
        mode = "Debug"
        args = "--target ALL_BUILD --config Debug"

    [[msvc-2019.build]]
        mode = "Release"
        args = "--target ALL_BUILD --config Release"

[clang-ninja]
    generator = "Ninja"

    [[clang-ninja.gen]]
        target = "Debug"
        out_dir = "build/Debug"
        args = "-DCMAKE_BUILD_TYPE=Debug"

    [[clang-ninja.gen]]
        target = "Release"
        out_dir = "build/Release"
        args = "-DCMAKE_BUILD_TYPE=Release"

    [[clang-ninja.build]]
        mode = ""
        args = "-- -j 8"
```

上面配置分别定义了两个 configuration：用于 Windows MSVC 的 `msvc-2019` 和用于 Linux 的 `clang-ninja`。

`*.gen` 描述的是 generation target，负责生成 build system files 的具体细节；`*.build` 描述则是 build mode，负责编译的具体细节。

安装好 anvil 后，只需要 `anvil gen msvc-2019` 就能够在目录 `build/msvc-2019` 中生成用于 VS 2019 的工程文件；而 `anvil build msvc-2019 --mode=Debug` 就可以触发 Debug 模式的编译。

这样一来，增加新的构建编译只需要增加一下对应的配置即可，无论是灵活性还是可扩展性，都有明显的提升。

### 0x02

在初步完工 anvil 之后，我已经拿 KBase 和 ezio 做起了实验，实际体验相比过去有明显的提升，尤其伴随着一次从 VS2017 升级到 VS2019 的过程，得益于分离配置的优点，可以很轻易地将构建编译流程升级到 VS2019。

anvil 目前已经基本处于 RC 阶段，后续稍微完善一下一些小坑（比如现在默认 VS Release 编译不产出PDB）之后基本可以作为正式的生产工具使用。

当然，GitHub 主页的 README 是需要先补充完整的，至少把 installation 和 quick start 给补齐了。