---
title: 利用 vcpkg 编译带汇编优化的 libx264
categories: PROGRAMMING
date: 2018-04-03 20:51:22
tags: [visual studio, windows, libx264, vcpkg]
---
使用 vcpkg 编译 libx264 有一个很重要的原因：可以获得 PDB，而且构建流程被大大精简了。

但是这里有一个坑：vcpkg 上的 libx264 模块编译默认是开启了 `--disable-asm`，意味着构建之后的二进制不会使用 SIMD 指令集，所以性能上会有很大的问题。

研究了一番之后找到了开启汇编优化的方案。

1. 从官网下载并安装 nasm-2.13

    vcpkg 上通过 acquire-program 提供的 nasm 只有 2.12；而编译 vcpkg 上提供的 libx264-152 需要 nasm-2.13

2. 编辑 libx264 的 portfile.cmake 文件

    在 set bash 的后面加一行
    ```cmake
    set(ENV{PATH} "c:/Programs/NASM/;$ENV{PATH}")
    ```
    这里注意替换你的真实安装目录

    然后去掉构建参数中的 `--disable-asm`

如果还有地方出错，记得根据提示查日志！

开启汇编优化后生成的二进制会略大，大概 900 多 KB

最后赞扬一下微软出品的 vcpkg，真是解决了 Windows 下编译一众开源软件的痛点。