---
title: 让 Path 更自然
date: 2016-04-19 00:38:38
categories: PROGRAMMING
tags: [path, c++, windows]
---
虽然 C++ 17 终于加入了对文件系统的支持，并且主流编译器的标准库也大都提供了一个 experimental implementation，但是就实际的反馈而言，当前的标准和实现都有点莫名其妙。

对，说的就是 `filesystem::path`。

对于一个 path class 而言，最常用的操作不外乎：`parent_path` `filename` `append/join`。

首先，对于函数 `parent_path()`，文档是这么描述的：


*Returns the path to the parent directory.
Returns empty path if empty() or there's only a single element in the path (begin() == --end().*

但是对于路径形如 `C:\` 的 `path` 对象，Visual Stuido 2015 update 2 提供的实现的调用 `parent_path()` 的结果居然是 `C:`。

好吧，看来这个实现版本认为 `C:` 是 `C:\` 的 parent path。

然而这个认为却是极大错误的。

实际上，`C:\` 才是和 Unix/Linux 下的 `/` 一样，是一个完整的根目录表示。而按照标准的要求描述，路径形如 `C:\` 的 parent path 应该是一个空字符串。

不过这估计也得怪 Windows 奇葩的路径设计。谁能想到 `C:tmp.txt` 和 `C:\tmp.txt` 是两个完全不同的路径表示呢？更别提还存在 `\\server\path` 这种 UNC 路径格式，以及必杀招：用以支持超过 `MAX_PATH` 的长路径的 `\\?\C:\very-long-path` 格式。

而对于 `filename`，实现上倒是没什么原则性的问题，但是 example 里反映的

```c++
fs::path("/foo/bar").filename(); // output bar
fs::path("/foo/bar/").filename(); // output .
```

给人一种刻板过头的感觉。你确定路径后面拖着的那个 trailing separator 是我可以要用来表示`.`的么？

不过也可能这是 unix/linux 下的一种神奇的用法，不过实在理解不能。

`filesystem::path` 不光提供了标准的 append，还顺带提供了纯字符串操作的 concat。抛开两个操作对应的符号重载是否美观不谈，操作 concat 的引入总给人一种 *I wanna to make it complete* 的无力感。

不过这个操作符对前面分辨 `C:/C:\` 不能倒是提供了一个帮助，Orz。

## 补救

很早之前在 `kbase` 里也提供了一个路径类的实现 `FilePath`。

在暂时无法接受 `filesystem::path` 的情况下，我对 `FilePath` 做了一次不大不小的重构。

首先把名字给换成了 `Path`，并且相关的几个操作也都保持和标准库一致。

但是这回 `Path` 的 `parent_path` 可是可以正确处理上面说的 dark cases 的；并且 `filename` 会在返回数据前机智的去掉 redundant trailing separator。

重复一遍，`C:\` 的 `\` 可不是 redundant trailing separator。

另外，`filesystem::path` 提供了一个 iterator 用来遍历路径的每个 component；而且实际上，`parent_path` 和 `filename` 都是直接用这个 iterator 构建的。

不过因为时间所限，我没有在 `Path` 里加上这个 iterator。（我怕强迫症犯了一天内肯定写不完）不过也仍然提供了一个类似的操作 `GetComponents`。

不同之处是这个函数会一下返回所有的 components，于是显得不那么 C++ philosophi compliant。不过还是将就着用吧，毕竟没有标准库实现那样有神奇的结果。

最新的代码可见 [这里](https://github.com/kingsamchen/KBase/blob/master/kbase/path.h) 和 [这里](https://github.com/kingsamchen/KBase/blob/master/kbase/path.cpp)；实例参见附带的 [unittest case](https://github.com/kingsamchen/KBase/blob/master/KBase_Test/samples/path_unittest.cpp)。

BTW：我删除了 kbase 的 dev 分支，打算换一种 git workflow。原来的 branch model 感觉有点不够拥抱变化。