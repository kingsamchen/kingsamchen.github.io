---
title: 解决 CMake 依赖工程中同名 cmake 函数调用混乱
categories: PROGRAMMING
date: 2019-08-18 20:52:04
tags: [cmake, anvil]
---
拿 learn-asio 这个项目练手时发现一个问题：learn-asio 依赖了 KBase，这两个项目都是我用 anvil 进行托管的，所以两个项目的 cmake 目录里各自有一份 `compiler_*.cmake`。

因为 `compiler_*.cmake` 提供的函数 `apply_common_compiler_property_to_target()` 默认使用了非常严格的属性，导致构建 asio 练手工程出现很多 warning 和静态分析的错误，所以为了省事我把 learn-asio 的这个函数做了宽松化处理。

但是重新构建的时候发现没有效果。

检查了一圈之后发现这个函数的调用变成了 KBase 目录下的版本，猜想可能原因是我首先引入了当前工程目录下的这个 .cmake 模块文件；然后又通过加载依赖的方式引入了 KBase 的 .cmake 模块文件，导致同名函数直接被覆盖成了最后出现的依赖提供的版本。

研究了一下没想到怎么解决，于是看了一下 folly 的处理方式，惊讶地发现 folly 是直接把项目名称 hardcode 进了函数里...

最后仿照着给 anvil 做了一个 [patch](https://github.com/kingsamchen/anvil/commit/dca6eaf97bcba4b930bfc4c3dcc64312d6a70a35)
