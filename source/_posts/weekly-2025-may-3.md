---
title: 一周杂记 in Week 3 May 2025
categories: CODE-LIFE
date: 2025-05-18 23:45:06
tags: [杂记]
---
本周（05/12 ~ 05/18）是5月份第三周，距离月底端午假期还有两周，而且马上就要陪产假了

## Life

\#1

这周五晚上和 Hogan 约了乐刻的 Body Combat 团课，周六跑了一次常规的 7.5km 有氧，周日继续 Body Pump 团课。

感觉这周强度有点大，还是需要好好休息以快速恢复体力和维持状态。

\#2

这周五把媳妇儿接回家之后媳妇儿就进入产假状态了，所以下周也不用早起送她去上下班了，对我来说算是个利好。

毕竟天天 6:55 起床不是每次都能保证睡眠良好有充足的体能的。

有一次我记得就睡了六个半小时，嘉明的 body battery 显示只恢复到了48还是44就起来了。果不其然这一天都感觉累的要死。

周日整理好东西之后开车送老婆去她家，进入待产状态。

不过老婆发现她家的床垫实在太硬了她睡得关节疼 😂 现阶段只能先忍忍了。。。

\#3

周三电影俱乐部和同事一起看了 warfare

- **warfare** 3/5 就还行把，没什么突出的点也没有什么太烂的点，非常不加兰

## Work

\#1

**CppCon 2022 | Fast, High-Quality Pseudo-Random Numbers for Non-Cryptographers in C++ - Roth Michaels** https://www.youtube.com/watch?v=I5UY3yb0128&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=118

- 某些非密码学安全级别得场合，可以使用 xorshiro128+ 这种更快的 PRNG；talk 里，xorshiro128+  要比标准库的 mt 快好几倍；不过 seeding 是需要专门处理
- 搭配标准库自带得 distribution 即可

**C++ Weekly - Ep 479 - final (and when to use it)** https://www.youtube.com/watch?v=NoFQnqUZLdA

- 当从某个类A派生是一个 program error 的时候用 final
- 用 final 可能会导致性能提升也可能会有下降，所以不要无脑用；视频里给了一个有提升的例子，编译器确信这个类的虚函数不会有派生类重写，所以就不用走虚表了

**C++ Weekly - Ep 346 - C++23's bind_back** https://www.youtube.com/watch?v=pDiP2frdMnI

- C++ 20 引入了 bind_front，于是 C++ 23 也有了 bind_back
- 区别就是 bind_back 的参数 params… 按顺序填充到参数尾，即 bind_back(fn(a, b, c), x, y)(z) 等价于 fn(z, x, y)；注意 x y 还是按顺序
- bind_back 和 bind_front 一样实现都是利用了 lambda 搭配 variadic templates

**C++ Weekly - Ep 345 - No Networking in C++20 or C++23! Now What?** https://www.youtube.com/watch?v=v6m70HyI0XE

- 实际就是可以从 conan/vcpkg 安装的一些网络相关的 lib 的介绍

**C++ Weekly - Ep 344 - decltype(auto): An Overview of How, Why and Where** https://www.youtube.com/watch?v=E5L66fkNlpE

- 介绍 `decltype(auto)` ；作为函数返回类型的时候，返回值，返回类型就会推导为值类型，引用的话就会推导成引用，可以作为 perfect returning 的用法（和 perferct forwarding 对应）

**C++ Weekly - Ep 343 - Digging Into Type Erasure** https://www.youtube.com/watch?v=iMzEUdacznQ

- 展示了一下一个简单的 type erasure 怎么做
- 感觉这个 sample 亮瞎了，值得吃透，实际的 impl 通过 captureless lambda 简化好多

**Go: the Good, the Bad and the Ugly** https://www.bluxte.net/musings/2018/04/10/go-good-bad-ugly/

- 这篇长文还不错，讲了很多 golang 的优点和设计上的坑点
- 坑点中我觉得大多数都还好，毕竟搞后端业务来来回回就那些，别用 golang 做一些不合适的需求就行；不过那个 interface 存一个 nil 但是 interface 自己不为 nil 确实挺坑的，还好日常中会遇到的概率低

**GCC's new fortification level: The gains and costs** https://developers.redhat.com/articles/2022/09/17/gccs-new-fortification-level#

- gcc-12 开始引入 `_FORTIFY_SOURCE=3` 可以对 glibc 和一些内建库做加固；level-3 是有 runtime check，引入了一点 runtime overhead 但是 cover 的范围更广了

**Reducing Signed and Unsigned Mismatches with std::ssize()** https://www.cppstories.com/2022/ssize-cpp20/

- 没啥好说的，就是引入了 `std::ssize()` 可以把容器的大小返回为有符号

\#2

在给 http-router 的 locate 加 tests 时发现了很早之前写的 insert path 部分的逻辑有问题，也不知道当初是咋写的。。。

在老婆家用笔记本配合 vscode 的 mscpptool debugger 定位到了问题，然后把相关的代码给 fix 了

---

好了这周就这样，下周见
