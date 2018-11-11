---
title: MSVC 对多继承下的 EBO 支持的一个 workaround
categories: PROGRAMMING
date: 2018-11-11 10:44:37
tags: [c++, msvc, ebo, multiple inheritance]
---
首先简单介绍一下 EBO（Empty Base Class Optimization）。

因为 C++ 规定，任何一个 instance 在内存中必须要有唯一的地址，因此一个空的 class/struct 会在编译时被偷偷插入一个外人看不到的 `char mem;`，于是这个空类的每一个 instance 都可以有一个唯一的地址了。

但是如果将这个空类作为某个类的成员时，这个隐藏的成员会被计入内存布局之中，考虑到 memory padding，有时候会导致类对象体积膨涨一倍。

例如考虑：

```cpp
class E {};

class A1 {
    E e;
    int i;
};

assert(sizeof(E) == 1);
assert(sizeof(A1) == 8);
```

我们会发现每一个 `A1` 的 instance 都占了 8-byte，比起 4-byte 足足翻了一倍。

占用内存无端变大导致 cache 问题啥的就不讲了，这方面的内容任何讲 computer architecture 的书应该都会有。
<!-- more -->
C++ 对此的一个解决方法就是 EBO，即通过继承来压缩空类的内存布局：

```cpp
class A2 : E {
    int i;
};

assert(sizeof(A2) == 4);
```

但是 EBO 的实现在 MSVC 上长久以来一直都有一个问题：如果涉及到多继承的情况，那么 EBO 便不能正常实施；which means

```cpp
class E1 {};

class E2 {};

class A3 : E1, E2  {
    int i;
};

assert(sizeof(A3) == 4); // Failure
```

这个问题也导致 MSVC 一直被同行嘲笑（可怜的孩子）。

最后终于在 2016 年 3 月份，MSVC 团队阶段性地解决了这个问题：引入了 `__declspec(empty_bases)`。

```cpp
class __declspec(empty_bases) A3 : E1, E2  {
    int i;
};

assert(sizeof(A3) == 4); // Now it succeeds
```

这个 workaround 从 Visual Studio 2015 Update 2 开始都可以使用。

至于为什么还要手动加 spec 指定，团队的说法是：这个修改是 ABI-incompatible 的（毕竟改了内存布局...），在最近几个版本需要保持 ABI 兼容的前提下，只能通过采用 spec 来做到 per-class-enabled 的程度，否则团队估计要被客服干死。

详细的内容可以看这里：[Optimizing the Layout of Empty Base Classes in VS2015 Update 2](https://blogs.msdn.microsoft.com/vcblog/2016/03/30/optimizing-the-layout-of-empty-base-classes-in-vs2015-update-2-3/)

另外，因为 Visual Studio 2017 在定位上要和 2015 保持 ABI 兼容，因此至少在 2017 上，还是要用过这个 spec 才能保证多继承下的 EBO 正确开展。

最后有人可能要问了：C++ 什么时候会需要一个没有成员的类呢？

答案其实不好回答，只能说需要的地方多了去了。

比如各种 tag-dispatch，traits 啊啥的，最经典的一个当属 `unique_ptr`：

如果不使用 customized deletor，那么就没有必要给 deletor 分配空间，这样一个 `unique_ptr` member 就可以和 trivial pointer 一样只占用 cpu word 大小的体积。

这一切是通过一个形如 `compressed_pair` 完成的，这个实现就用到了 EBO。
