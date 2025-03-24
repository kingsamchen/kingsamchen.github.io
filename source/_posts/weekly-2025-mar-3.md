---
title: 一周杂记 in Week 3 Mar 2025
categories: CODE-LIFE
date: 2025-03-24 23:54:31
tags:
---
本周（03/17 ~ 03/23）是3月份第三周，天气短暂的开始转热，感觉已经要一秒入夏了。

## Life

\#1

周一的时候兴致冲冲去家附近的乐刻精品店打算接着跑步，结果发现他们家网络又又又出问题了。。。

无奈骑电驴跑到了古墩路印象城的乐刻。

虽然这家店面积比城西银泰大多了，跑步机的水平位置也没问题，但是调了配速之后发现这个跑步机的速度上限应该被锁到了9kmh 左右，配速后面怎么都上不去了

不过起码能跑，所以只能改用手表的历程记录了，毕竟不能自欺欺人 🤡

\#2

周三的时候媳妇儿说已经和丈母娘说同意去萧山医院那家月子中心了，于是丈母娘火速联系了当天的护士，签立了合同也付了定金。

然后约等于我们三（我，我妈，他妈）都松了一口气。

后面终于不用当主C了。

\#3

周四把车送去保养了，换了个原厂的21寸马牌轮胎，近2000，确实有点小贵。

右后车门那边轻微划痕做了一点抛光但是还是能看出来。不过暂时先这样吧，不然用油漆券补这点漆估计有点亏。

用了一张取送券，换完轮胎后周五上午代驾直接开到地库车位，确实方便。

不过晚上检查的时候发现洗车不是很细致啊，一些角落的灰没清干净 🤡

\#4

周六中午吃了个大渔，吃撑了，下午跑步的时候非常不舒服。

因为家边上的乐刻还在修网络，又不想跑太远，所以去的城西银泰。

当天天气挺热的，但是健身房没有开冷气，通风也一般，加上中午吃的太多了，跑到第6km的时候实在跑不动了，最后1.5km基本是走完的。

说明以后中午真的不能吃太多了

## Work

\#1

**CppCon 2022 | Help! My Codebase has 5 JSON Libraries - How Generic Programming Rescued Me - Christopher McArthur** https://www.youtube.com/watch?v=Oq4NW5idmiI&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=96

- 业务上为了适配5个不同的 json lib，用 tmp（mainly type traits + SFINAE）做了一个编译期匹配检测的东西
- static members 用 is_detected（目前是 experimental）is_xxx；普通 member 得用 std::declval

**CppCon 2022 | Lightning Talk: Modernizing SFML in Cpp - Chris Thrasher** https://www.youtube.com/watch?v=JJPL17sDxUs&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=94

- 演讲者发起的一个活动吧，用 modern cpp 改造 sfml 这个框架

**CppCon 2022 | C++20’s [[likely]] Attribute - Optimizations, Pessimizations, and [[unlikely]] Consequences** https://www.youtube.com/watch?v=RjPK3HKcouA&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=93

- likely 和 unlikely 编译器很早就有 built-in intrinsic 这次只是标准化了；他们的作用主要是 hint 让编译器生成更合适的代码
- 编译器自己会有 branch predictor 优化，以及 CPU 自身也有 branch predictor 的一些 on-the-fly 优化
- 这些 hints 都不会影响 CPU 自身的 branch predictor，只能说是编译器尽量辅助贴近 CPU 的特质
- likely 对 loop 没啥用，但是unlikely 对loop 用得不对有负面影响；不过clang目前生成的代码没有差别
- 谨慎使用这个attributes，绝大多数时候编译器的选择是对的，用错反而会造成性能降低

**CppNow 2024 | Mistakes to Avoid When Writing C++ Projects - Bret Brown** https://www.youtube.com/watch?v=UrU8O1mMyNE&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=45

- 来自 bloomberg 的分享 C++ project 开始的一些注意点
- sample repo https://github.com/bretbrownjr/zerocode
- 对于构建工具和包管理那部分感觉有点意思，包括一些参数不应该直接 hardcoded 而是通过外部注入，这种明显是有大型混合项目经验的

**CppNow 2024 | Generic Arity: Definition-Checked Variadics in Carbon - Geoffrey Romer** https://www.youtube.com/watch?v=Y_px536l_80&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=43

- 介绍的 carbon-lang 的 variadic type checking
- 可以当休闲

**C++ Weekly - Ep 471 - C++26's =delete With a Reason** https://www.youtube.com/watch?v=9iFJRCHwSok

- C++ 26 允许 `= delete("reason why to delete")`

**C++ Weekly - Ep 379 - clang-tidy's "Easily Swappable Parameters" Warning - And How to Fix It!** https://www.youtube.com/watch?v=Zq4yYPG7Erc

- 这个 tidy 我觉得只对 library designer 有参考意义，而且意义有限，实际应用体验很差；有时候甚至不如用 ut 解决
- 文中第一个 strong type 的方案第一是很繁琐，而且这些类型很 ad-hoc；第二个方案 `Range{1, 100}` 我是不是可以说这个构造函数可能也写错呢，毕竟两个都是 int ？

**C++ Weekly - Ep 376 - Ultimate CMake C++ Starter Template (2023 Updates)** https://www.youtube.com/watch?v=ucl0cw9X3e8

- jason 自己搞得 project template

**C++ Weekly - Ep 378 - Should You Ever std::move An std::array?** https://www.youtube.com/watch?v=56DMwqKffi0

- 算是一个 common sense，array 不能被移动，最多里面的每个元素被移动
- 如果元素也不能被移动就退化成了拷贝
- 不过如果是前者，采用更 explicit 的方式感觉要更好一些

**C++ Weekly - Ep 377 - Looking Forward to C++26: What C++ Needs Next** https://www.youtube.com/watch?v=L4PqCIMmc-A

- Jason 自己期待的 26 的一些特性
    - rest/more constexpr
    - pattern matching
    - contracts
    - reflection, especially enum ↔ string
    - no more implicit conversion in standard library

**C++ Weekly - Ep 375 - Using IPO and LTO to Catch UB, ODR, and ABI Issues** https://www.youtube.com/watch?v=Ii-zuK1cd90

- 用 LTO 来处理 ODR-vioaltion，有点意思…就是会不会有点杀鸡用牛刀啊

**Getting rid of unwanted branches with __builtin_unreachable()** https://nicula.xyz/2025/02/23/unwanted-branches.html

- 利用 `__builtin_unreachable()` (对应到 C++ 23 的 `std::unreachable()` ）来做编译器的激进优化
- 不过这个太 low-level 和 tricky ，基本是得跟着你的 bottleneck 的上下文特征来
- 文中发现输入参数从 ptr + len 换成了 std::vector 都会导致 GCC 下的优化失败…

**Making my debug build run 100x faster so that it is finally usable** https://gaultier.github.io/blog/making_my_debug_build_run_100_times_faster.html

- 为了一叠粗（SIMD）做了一盘饺子
- 嫌弃 debug build 下 asan byte-by-byte  处理拖慢了 sha1 computation

**7 Interesting (and Powerful) Uses for C++ Iterators** https://johnfarrier.com/7-interesting-and-powerful-uses-for-c-iterators/?utm_source=rss&utm_medium=rss&utm_campaign=7-interesting-and-powerful-uses-for-c-iterators

- 内容实用性一般，很多解法都有更好的。。摊手

**asio如何处理eof错误** https://mp.weixin.qq.com/s?__biz=MzIxMzY5MzY4MQ==&mid=2247484517&idx=1&sn=5d7325eeb6d0812ab68c8bb002ebb515&chksm=97b3a1fba0c428ed516c98ac9435e03936060d4e462c30a903e9eecf2893b9f1b4bd21279e71

- 这篇虽然短的，但是想了一下是有道理的
- TCP 的关闭确实是个老大难问题，shutdown write 会进入 half-closed 状态，虽然对端FIN包都发了但是实际上还能读
- 这篇的核心就是考虑到对端可能专门有 half-closed 需要处理，读到 eof 的时候如果还有数据没发完就先发再 shutdown
- muduo里我记得也有类似的处理逻辑

\#2

跟着 Jason 的视频学习了一下大概知道了怎么用 CMake 启用 LTO 了

```cmake
include(CheckIPOSupported)
check_ipo_supported(RESULT supported OUTPUT error) # hard error if LTO not supported

set_target_properties(foobar PROPERTIES INTERPROCEDURAL_OPTIMIZATION ON)
```
