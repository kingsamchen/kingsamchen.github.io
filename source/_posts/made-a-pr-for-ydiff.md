---
title: 给 ydiff 提了个 PR
categories: CODE-LIFE
date: 2018-09-26 23:50:36
tags: [pull request, diff, python]
---
[ydiff](https://github.com/ymattw/ydiff) 是基于 CLI 的一个 diff tool，支持 side-by-side 模式

前段时间在 surface 上 pip 安装后发现不能使用，直接提示找不到 `SIGPIPE`；看来这玩意儿压根没考虑过支持 Windows。

因为觉得这个程序有点好玩，所以花了点时间对源代码做了一点修改，增加了对 Windows 的支持。

（其实就是做了一下 SIGPIPE 的兼容问题）

然后给作者提了一个 PR：https://github.com/ymattw/ydiff/pull/81

最后赞一下 python 内建的 decorator 机制，做非侵入式的函数 hook 还是很便捷的。