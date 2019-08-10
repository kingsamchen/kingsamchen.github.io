---
title: 安利 asio-cmake
categories: PROGRAMMING
date: 2019-08-10 16:33:28
tags: [cpp, asio, cmake]
---

## 0x00 Background

考虑到 ASIO 的相当一部分组件（subset）已经进入了 C++ 20 Networking TS，未来再经过一些小调整之后成为标准库的一员基本板上钉钉。

那么提前熟悉一下 ASIO 也不是什么坏事，毕竟没准等到 C++ 23 的时候，coroutine, networking, executor 啥的都已经很完备了，谁都可以用 C++ 实现一些性能不差的 server-end 代码了（比如 RPC 框架？）。

不过和使用 Boost 类似，使用 ASIO 作为依赖库也不是一件容易的事情；毕竟自 modern C++ 元年至今都8年了，CMake 还在成为 de facto building system 的道路上挣扎，广泛接受的包管理依然不见踪影...

## 0x01 ASIO-CMake Wrapper

陈年老账就不再扯了，得益于 CMake 3.2 开始提倡的 taget-based properties 和 CMake 3.11 开始提供的 `FetchContent` module，我花了一些时间实现了一个基于 `FetchContent` 的 asio-cmake wrapper。
<!-- more -->
这个 wrapper 本质就是一个 `CMakeLists.txt` 文件，它能够

1. 下载指定版本的 ASIO 源码

2. 创建一个 `asio` target，并且将刚才下载的源码设置为 `asio` 的 `target_include_directories`。因为 ASIO 是 header only library，所以这里只需要暴露头文件即可

3. 为 `asio` 这个 taget 进行必要的预设置，例如宏定义 `ASIO_STANDALONE` ，POSIX 环境下链接 `pthread` 等等

对于需要依赖 ASIO 的工程，只需要通过 `add_subdirectory()` 的方式引入包含这个 `CMakeLists.txt` 文件的目录，就可以获得前面定义好的 `asio` 这个targe；然后只需要加入到 `target_link_libraries` 列表里就完事了。

## 0x02 Sample

直接举个例子：

假设我们有一个 `asio_demo` 工程需要依赖 ASIO，我们把 asio-cmake 文件放到工程的 `asio_dep` 目录中，结构如下：

```
asio_demo
│-- CMakeLists.txt
│-- main.cpp
│
└─asio_dep
  |
  └─ CMakeLists.txt	# our wrapper file
```

那我们只需要在顶层 `CMakeLists.txt` 中实现如下定义即可：

```cmake
cmake_minimum_required(VERSION 3.12)

project(asio-demo)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# specify asio src version.
set(ASIO_CMAKE_ASIO_TAG asio-1-12-2)
add_subdirectory(asio_dep)

add_executable(asio_demo main.cpp)

target_link_libraries(asio_demo PRIVATE asio)
```

剩下的事情 CMake 都会帮你弄好。

BTW：如果你在中国大陆，记的先设置代理 🤐，毕竟 ASIO 源码说小不小。

## 0x03 Extra Bonus

这个做法有两个额外的优点。

第一个优点是，因为 asio-cmake 本身也是一个 git repository，所以你甚至可以利用 `FetchContent` module 直接拉取 asio-cmake 作为你的 dep，然后 asio-cmake 内部处理的时候会进行 ASIO 源码下载和属性设置。

```cmake
include(FetchContent)

FetchContent_Declare(asio-cmake
  GIT_REPOSITORY https://github.com/kingsamchen/asio-cmake.git
  GIT_TAG        origin/master
)

# Specify asio version
set(ASIO_CMAKE_ASIO_TAG asio-1-12-2)
FetchContent_MakeAvailable(asio-cmake)

# ...

target_link_libraries(proj-name
  PRIVATE asio
)
```

第二个优点比较润物细无声。

虽然 ASIO 自身并没有提供 cmake 支持，但是基于 Modern CMake 提倡的 target-based properties 机制和 `FetchContent` 模块，我们事实上可以给任意项目提供一个类似的 cmake-wrapper。

比如给 Boost 提供一个模块粒度的 wrapper。

## 0x04 Misc

由于我个人是 C++ 源码依赖的拥趸，因此源码依赖对我来说在这里不是一个缺点。

项目地址：https://github.com/kingsamchen/asio-cmake

欢迎 PR。

最后如同我在 README 文件里说的：

>  Make ASIO && C++ Great Again

