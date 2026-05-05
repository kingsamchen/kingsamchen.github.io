---
title: 一周杂记 in Week 5 Apr 2026
categories: CODE-LIFE
date: 2026-05-05 10:01:22
tags: [杂记]
---
本周（04/27 ~ 05/03）是四月份最后一周，周五开始就是五一假期。

## Life

\#1

因为新办公室气味问题，这周继续居家办公，五一之后才需要回办公室

好处自然是少了通勤时间，感觉贼爽，而且不用老看着老催，心情自然好不少。

缺点就是吃饭有点麻烦，而且秋宝有时候确实会干扰工作思考，主要还是秋宝太可爱了。

\#2

4/29 的中午早早吃完中饭后开车送媳妇儿去邵逸夫博士面试，老婆下车时候还把手机落了。。。

当天下着雨邵逸夫周围停车贼难，还好老婆用护士台电话联系我说结束之后可以用别人手机联系我。

结束之后我问老婆结果，答曰回家再说，所以一开始以为挂了，结果没想到最后是被邵逸夫心内大主任选上了，不知道后面9月份开始是不是要没休息卷学业了…

\#3

周五 5/1 开启五一假期，一大早带着娃回温州老家

六点半早早起床整理东西，结果秋宝自己睡得香的不得了完全没有要醒的样子，所以我们只能把她叫醒，然后哄哄吃完早奶，迟了半小时才出发。

先开车送老婆岳母小宝到东站，然后我再开回老婆医院停车，最后一站地铁到东站。

因为这次直接停医院内停车场，地铁站就在边上，所以我到东站也很快，提前三十多分钟就到了

然后就是一路高铁一等座晃悠悠到了老家

秋宝到老家之后也不凑巧，隔壁和对门都有老人过世，吹吹打打持续了三天，5.4才开始安静下来....

不过老婆到是非常喜欢回老家

\#4

飞驰人生3 看了一部分，目前感觉不如2。。。

## Work

\#1

**C++ Weekly - Ep 530 - Clang's New Constant Interpreter?** https://www.youtube.com/watch?v=tUuOx6xlXFo

- 介绍的 Clang 23 引入的 `-fexperimental-new-constant-interpreter` https://clang.llvm.org/docs/ConstantInterpreter.html
- 号称可以让 constexpr 相关的代码编译的时候更快

**C++ Weekly - Ep 151 - C++20's Lambdas As Custom Comparators** https://www.youtube.com/watch?v=damrgf7GJac

- std::set 这些容器可以提供一个类型来做 custom comparator，但是比较震惊是C++20开始才支持用 decltype(lambda) 这么干嘛
- 不过如果用的比较多，可能 explicitly define a type 会更好

**C++ Weekly - Ep 150 - C++20's Lambdas For Resource Management** https://www.youtube.com/watch?v=XhxV1NP5RGs

- 省流，cxx 20 开始 lambda 支持 default init
- 所以 unique_ptr 的 deleter 可以直接内部构建了

**C++ Weekly - Ep 149 - C++20's Lambda Usability Changes** https://www.youtube.com/watch?v=JxYD8_OHQg8

- c++ 20 开始 stateless lambda 允许 default constructible & copy assignable；并且也允许 within unevaluated context e.g. in `decltype()`
- c++ 17 要复制一个 stateless lambda 只能 `auto n_l = l;`

**C++ Weekly - Ep 147 - C++ And Python Tooling** https://www.youtube.com/watch?v=ZsKdRtQM7EA

- 省流：使用 pip 可以在一些老旧的系统上安装比较新的 c++ toolchain，例如 cmake / ninja .etc

**CppCon 2023 | Writing Python Bindings for C++ Libraries: Easy-to-use Performance - Saksham Sharma** https://www.youtube.com/watch?v=rB7c69Z5Kus&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=38

- 使用 boost.python / pybind11 做 c++ → python 的 interop

**严格别名（Strict Aliasing）规则是什么，编译器为什么不做我想做的事？**https://zhuanlan.zhihu.com/p/595286568

- 介绍 strict aliasing，以及 C/C++ 编译器引入这个假设两块输入内存地址并不存在 alias 关系以为了做更好的优化，但是很多时候会导致符合直觉的代码产生明显的UB
- 不过其实不写 UB 的代码的话可以少很大一部分错误的可能，剩下一部分只能在写的时候反复注意了，必要时还得看汇编；盲目的上 `-fno-strict-aliasing` 也不是一个好办法
- 另外提到 C 和 C++ 的主要发明者都对 default strict aliasing 颇有微词

\#2

这周在 cursor/opus 的辅助下，成功把 scheduler 所有的模块都做了 CMake 的构建支持，包括那个非常令人头疼的 httpd mod_ws 模块。

不过中间还是踩了 httpd 的 so 构建坑，还好最后还是搞定了。

第一个坑是，除了 httpd so 之外其他库都是静态库，这些静态库依赖的 PRIVATE linked libraries 实际上并不会链接到静态库本身，如果最终产物是 executable 到还好，但是对于 dynamic lib，这些 intermediate libs 的符号会变成 Unresolved 的存到 so 中，直到启动时才会提示失败。。。

因为 so 本身是 httpd 通过 dlopen 加载的，所以不可能强制 resolve all symbols，所以最后想了一下，保留 libscheduler 为动态库，并把所有 intermediate static libs 的 linked libraries 都改成 PUBLIC，强制参与 libscheduler 的链接。

另外一个坑是，libschecduler 的 test binary 同时链接了一个基础 .a 和 libscheduler.so，一些符号重复定义了，导致 gtest discovery 直接触发 segfault...

解决方案是，libscheduler.so 构建引入一层 .o bundle 的 OBJECT，test binary 链接 OBJECT target，不要链接 .so

---

另外因为发现链接几个 consumer executable 的时候 gnu ld 内存吃得有点离谱，默认8 cores ninja会 spawn 10 个 job，导致10个 ld 直接把 vagrant vm 的内存吃完了（8C 16G）。

所以想着要不直接做 mold linker 的支持得了。

因为已经切到换了 CMake 所以 mold 支持也容易很多，不过因为目前 gcc-10 不认识 ld.mold，所以必须强制写 add_link_options 指定 `-B <path/to/mold/root>/libexec/mold` 这个目录位置，让 gcc-10 去 consume。

换上 mold 之后，链接速度肉眼可见快了非常多

---

好了这周就这样，下周见
