---
title: C++ 工程依赖管理新方向：CMake & Git
categories: PROGRAMMING
date: 2019-02-10 14:52:21
tags: [c++, dependency management, cmake, git]
---
本文内容中提及的 CMake 均指提倡 target-based properties 的 [modern cmake](https://kingsamchen.github.io/2018/06/19/modern-cmake/)，而非史前版本的 legacy cmake。

### The Right Way: 源码依赖

对于 C++ 工程而言，只要 ABI 的问题还存在，源码依赖就是最稳妥最普适最可靠的依赖引入方式；即使这些引入的源码在构建中会单独编译成（动/静态）库。

同时，GitHub 成为开源文化社区的标杆后，获取实现了某一功能的第三方库的源代码的难度大大降低。

因此个人倾向上：只要允许，都应该以特定版本的源码引入的方式去依赖一个第三方库。

事实上，Google Facebook 这些大厂内部实行的 monorepo 也是源码依赖的一种实现方式，因为某个工程需要的依赖源码都可以一并获取到。

在使用 CMake 作为构建系统的工程体系下，要以源码依赖的方式添加一个子工程只需要使用 `add_subdirectory()` 添加目标工程的顶层目录（根 `CMakeLists.txt` 所在的目录）。

### Git Submodule: 一次不完美的尝试

我的个人项目 KBase 和 ezio，在此之前都是通过 git submodule 的方式引入自己需要的依赖源代码，然后通过

- Visual Studio 子工程添加到解决方案（Windows 平台）
- CMake `add_subdirectory()` 建联（*nix 平台）

依赖的版本管理直接复用 submodule 提供的特性。
<!-- more -->
使用 git submodule 这个方案有两个比较明显的问题：

1. submodule 在 VCS 这个级别上增加了你的项目和依赖之间的耦合，个人感觉侵入程度过重且产生的关系很奇怪；且 git submodule 实在不能说是一个很好用的功能，日常使用它也有一些额外的艰险。

2. 需要仔细考虑工程文件的 organization & layout。

   举例来说：

   1. KBase 依赖了 Catch2 作为其单元测试框架
   2. ezio 依赖了 KBase 作为其基础库；同时也依赖 Catch2 作为其单元测试框架
   3. 某一天我要写一个目标为可执行文件的项目 Eureka，Eureka 需要 KBase 作为基础库，ezio 作为其网络库，同时还要 Catch2 作为其单元测试框架

   如果要使用 git submodule 我很难想象要如何操作才能保证整个项目下各自工程源码只有一份。

个人理解：git submodule 更像是 monorepo 模式的一种偏特化（partial specialization）。monorepo 通过一个全能视角一下碾平所有工程之间的依赖关系。submodule 和 monorepo 则类似，可以直接做代码提交并推送到源 repo。

### CMake 革命性的转折点: FetchContent

[FetchContent](https://cmake.org/cmake/help/latest/module/FetchContent.html) 是 CMake 自 3.11（大约发行于2018年4月份左右）开始引入的一个模块。这个模块可以看成是原来 `ExternalProject` 模块的一个升级。

> This module enables populating content at configure time via any method supported by the [`ExternalProject`](https://cmake.org/cmake/help/latest/module/ExternalProject.html#module:ExternalProject) module. Whereas [`ExternalProject_Add()`](https://cmake.org/cmake/help/latest/module/ExternalProject.html#command:externalproject_add) downloads at build time, the `FetchContent`module makes content available immediately, **allowing the configure step to use the content in commands** like [`add_subdirectory()`](https://cmake.org/cmake/help/latest/command/add_subdirectory.html#command:add_subdirectory), [`include()`](https://cmake.org/cmake/help/latest/command/include.html#command:include) or [`file()`](https://cmake.org/cmake/help/latest/command/file.html#command:file) operations.

上面重点部分加粗了。

简言之，使用 FetchContent 模块可以在 configuration stage 将某个依赖（content）下载并设置好，然后通过 `add_subdirectory()` 等命令引用。

对于多个 project 依赖同一个 content 的情况，FetchContent 会自动进行处理：

> The ability to detect whether content has already been populated ensures that even if multiple child projects want certain content to be available, the first one to populate it wins. The other child project can simply make use of the already available content instead of repeating the population for itself.

对于依赖（content）的来源，FetchContent 和他的前辈一样，支持 Git, SVN 等一众 VCS ；对于使用 Git 的依赖来说，只要指定 repo 地址、tag（可以是 git tag、分支名，也可以是 commit hash），FetchContent 就能为你拉取目标依赖的源代码以及帮你进行基本的 configuration。

在这个机制下，export / install / find-package 基本上可以不用考虑了。

注：FetchContent 模块的更新在这几个版本比较频繁，个人猜测这个模块可能是 CMake 未来迭代的核心点之一。

综合以上上面所述，可以发现，FetchContent + Git **基本实现了一个包管理器的基础功能**，并且因为 Git 的分布式特点，这个包管理器也是**分布式**的。

### Real World Use Case

经过春节几天在家的研究，我找到了一种基于 CMake + Git 的 C++ 依赖管理模式。

下面以 ezio 为例，简单阐述一下如何使用这种模式。

ezio project 包含 ezio lib 和 unittests 两部分；前者需要**引入**对 KBase 基础库的依赖，后者需要引入对 Catch2 的依赖；同时，KBase 也需要引入对 Catch2 的依赖，并且这部分的依赖引入对 ezio 来说应该是透明的。

注：上面虽然 unittests 也要依赖 KBase，但是因为 unittests 首先要显式引入依赖 ezio，而 ezio 已经引入了依赖 KBase，所以 unittests 不需要自己去引入对 KBase 的依赖。

整体工程文件结构如下：

```
├── build
├── cmake
├── CMakeLists.txt
├── deps
     ├── FetchCatch2.cmake
     └── FetchKBase.cmake
├── examples
├── ezio
├── gen.py
├── LICENSE
├── README.md
└── tests
```

相比使用 git submodule 的做法，直接移除了依赖源码，取而代之的是 `deps` 文件夹里的 cmake 脚本。每个脚本里都用 FetchContent 获取依赖内容。

因为 ezio lib 需要引入 KBase lib，所以在它的 `CMakeLists.txt` 里，有一行

```cmake
include(${EZIO_DEPS_DIR}/FetchKBase.cmake)
```

抽象语义上，运行完上面的脚本 KBase 就已经成为整体工程一员，所以外部不需要单独设置，直接使用依赖内容即可：

```cmake
target_link_libraries(ezio
  PRIVATE kbase
)
```

类似的，unittests 引入 Catch2, 它的 cmake 文件里也只需要

```cmake
include(${EZIO_DEPS_DIR}/FetchCatch2.cmake)

#...

target_link_libraries(ezio_test
  PRIVATE
    ezio
    kbase
    Catch2
)
```

运行 configuration 后，会有如下操作：

```
-- The CXX compiler identification is Clang 6.0.1
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- cotire 1.8.0 loaded.
-- BUILD_TYPE = DEBUG
-- Fetching dep: kbase-v0.1.3
-- kbase-v0.1.3 source dir: /home/kingsamchen/Projects/ezio/build/deps/kbase-v0.1.3-src
-- kbase-v0.1.3 binary dir: /home/kingsamchen/Projects/ezio/build/Debug/kbase-v0.1.3-build
-- BUILD_TYPE = DEBUG
-- CXX target kbase cotired.
-- KBASE_BUILD_UNITTESTS = ON
-- CXX target ezio cotired.
-- EZIO_BUILD_UNITTESTS = ON
-- Fetching dep: catch2-v2.5.0
-- catch2-v2.5.0 source dir: /home/kingsamchen/Projects/ezio/build/deps/catch2-v2.5.0-src
-- catch2-v2.5.0 binary dir: /home/kingsamchen/Projects/ezio/build/Debug/catch2-v2.5.0-build
-- CXX target ezio_test cotired.
-- EZIO_BUILD_EXAMPLES = ON
-- Configuring done
-- Generating done
```

依赖的源码被统一存放在了 `ezio/build/deps` 目录中；而对应的依赖构建则存放到 `ezio/build/Debug` 目录中。

两个目录结构直观看如下：

```
build/deps
├── catch2-v2.5.0-src
│   ├── appveyor.yml
│   ├── artwork
│   ├── CMake
│   ├── CMakeLists.txt
│   ├── codecov.yml
│   ├── CODE_OF_CONDUCT.md
│   ├── conanfile.py
│   ├── contrib
│   ├── docs
│   ├── examples
│   ├── include
│   ├── LICENSE.txt
│   ├── misc
│   ├── projects
│   ├── README.md
│   ├── scripts
│   ├── single_include
│   └── third_party
└── kbase-v0.1.3-src
    ├── build
    ├── cmake
    ├── CMakeLists.txt
    ├── deps
    ├── docs
    ├── gen.py
    ├── kbase
    ├── LICENSE
    ├── README.md
    └── tests
```

```
build/Debug
├── bin
│   ├── chat-client
│   ├── chat-server
│   ├── echo-client
│   ├── echo-server
│   ├── ezio_test
│   └── unity
├── build.ninja
├── catch2-v2.5.0-build
│   ├── CMakeFiles
│   ├── cmake_install.cmake
│   ├── CTestTestfile.cmake
│   ├── DartConfiguration.tcl
│   └── Testing
├── catch2-v2.5.0-subbuild
│   ├── build.ninja
│   ├── catch2-v2.5.0-populate-prefix
│   ├── CMakeCache.txt
│   ├── CMakeFiles
│   ├── cmake_install.cmake
│   ├── CMakeLists.txt
│   └── rules.ninja
├── CMakeCache.txt
├── CMakeFiles
│   ├── 3.12.1
│   ├── cmake.check_cache
│   ├── CMakeOutput.log
│   ├── CMakeTmp
│   ├── feature_tests.bin
│   ├── feature_tests.cxx
│   └── TargetDirectories.txt
├── cmake_install.cmake
├── examples
│   ├── chat
│   ├── CMakeFiles
│   ├── cmake_install.cmake
│   └── echo
├── ezio
│   ├── CMakeFiles
│   ├── cmake_install.cmake
│   ├── cotire
│   ├── ezio_CXX_cotire.cmake
│   └── ezio_CXX_Debug_cotire.cmake
├── kbase-v0.1.3-build
│   ├── CMakeFiles
│   ├── cmake_install.cmake
│   └── kbase
├── kbase-v0.1.3-subbuild
│   ├── build.ninja
│   ├── CMakeCache.txt
│   ├── CMakeFiles
│   ├── cmake_install.cmake
│   ├── CMakeLists.txt
│   ├── kbase-v0.1.3-populate-prefix
│   └── rules.ninja
├── lib
│   ├── libezio.a
│   ├── libkbase.a
│   └── unity
├── rules.ninja
└── tests
    ├── CMakeFiles
    ├── cmake_install.cmake
    ├── cotire
    ├── ezio_test_CXX_cotire.cmake
    └── ezio_test_CXX_Debug_cotire.cmake
```

两个依赖脚本被我大幅自定义过：

1. 默认模式下 src 和 build 两个目录是在同一个父目录中；而我调整后所有依赖的 src 在一个目录下，build 目录归属当前构建目录
2. 一个依赖 ID 由：名字 + tag 唯一构成
3. FetchContent 在进行 populate 前会首先通过代码检测目标文件是否已经存在，如果存在则提示 _kbase-v0.1.3 source dir is already ready; skip downloading_ 然后设置已存在的目录为目标依赖

上面三个点相辅相成。

原因：

- 我个人在 Linux 上使用 CLion 作为开发环境，CLion 目前只能使用 makefile 而我个人偏好 ninja。如果设置不同的 generator（例如 ninja 和 makefile）运行 configuration，依赖的源码会被 FetchContent 清空并重新下载。因为对某个 generator 来说，这份源码并不是它 configure 的。
- Windows 上也会使用 CMake 作为工具链，考虑到 WSL，两个环境使用同一份源码是最好的。于是加剧了第一点。

于是依赖脚本如果发现目标源码已经存在，那么就直接跳过 fetch 过程直接进行 populate；同时分离存放依赖源码和构建的目录，这样就可以解决上面两个问题。

依赖 ID 引入版本号的为了解决某个依赖升级后，因为目标源码已经存在 fetch 过程被跳过导致无法更新；同时如果依赖源码更新了而某一个 generator 不知道（因为名字上看不出），则后续的 configuration 会提示错误。

完整的配置可见：https://github.com/kingsamchen/ezio

### Recap

总结一下在这个模式下要引入一个依赖的步骤：

1. 实现依赖设置脚本 `FetchXXX.cmake`，存放到目录 `deps` 中

2. 在需要引入这个依赖的模块的 `CMakeLists.txt` 中使用

   ```cmake
   include(${EZIO_DEPS_DIR}/FetchXXX.cmake)
   ```

3. 使用 `target_link_libraries()` 等方式使用依赖即可

### Wraps Up

这个模式弄熟之后使用起来就很方便，且足够灵活，能够应付大多数场景。

但是细节上，这个模式目前来看有一个很大的缺点：因为 CMake 早前版本的各种设计问题，需要通过很多规范来避免出现问题；同时这些规范映射到具体的工程配置，都是大段类似的代码。

`deps` 目录下的 `FetchXXX.cmake` 内容其实大同小异；而不同工程的顶层 `CMakeLists.txt` 其实也是大同小异；都可以抽象出一个 base template。

目前这些文件的编写还是要靠人肉手写来完成，不过我觉得可以通过一个简单的 python 脚本 + 文件模板来就简化。

细想，如果未来这套方案成熟，并且随着 FetchContent 模块的迭代功能愈发完善，实现一套以个模式为基础的  C++ 依赖管理器应该不是很难的事情。