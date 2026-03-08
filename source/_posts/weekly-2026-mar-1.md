---
title: 一周杂记 in Week 1 Mar 2026
categories: CODE-LIFE
date: 2026-03-08 23:44:38
tags: [杂记]
---
本周（03/02 ~ 03/08）是三月份的第一周，似乎每年的3月份感觉起来都极其漫长。

## Life

\#1

娃这周又开始有点厌奶了，不管是我妈还是媳妇儿都没法回到之前一天的奶摄入量。

不确定是不是增加了一天两顿辅食之后，娃觉得奶没有那么好喝了 🤡

不过辅食倒是吃的欢，所以老婆的解决方案是买了一个更合适的小皮粗颗粒多一点的米糊，这样冲泡的时候用奶可以更多。

这样把奶量每日最低限度就可以压到600，而且辅食多了之后，摄入的东西也更多样化。

\#2

这周把鸭科夫全成就了，而且游戏内部的任务都打完了

![](/img/escape-from-duckov.jpg)

\#3

这周三参加了一次电影俱乐部，不过就我和 iker 了

- 电影 **Hamnet 3.5/5** 一开始以为是哈姆雷特传（莎士比亚传）看了3/4发现是莎士比亚夫人传…女性视角解读的这类题材最近几年是有点火哦，不过整体给人的冲击感讲道理是不如 Train Dreams 的

## Work

\#1

**CppNow 2025 | How To Affect the Future of C++ Standard in 90 Minutes - River Wu** https://www.youtube.com/watch?v=B0vPuR7sFl0&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=49

- 这个 talk 有点意思，演讲者是个华人
- 首先是说 cpp 有点老登语言了，吸引不到喜欢酷酷的年轻人，而且他的标准库或者一些有代表性的库啥的都写得“乱七八糟的”没法让年轻人学习，所以就引出 beman 这个最近很火的项目
- beman 的目标可以看成 modern boost，会提供那些很可能进了标准，或者已经进了标准但是距离各家提供还比较久远的提案的库实现
- 然后提了一些作者自己的学习心得

**CppNow 2025 | Are We There Yet? - The Future of C++ Software Development - Sean Parent** https://www.youtube.com/watch?v=RK3CEJRaznw&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=52

- 回顾过去和展望未来的系列，纯技术内容不多
- 提了一下当年 java dark age 时80%的人写不对 binary search，未来进入 ai dark age 会经历非常类似的事情
- 强调了一下 generic programming 的重要性，还和数学证明做了对比
- 展望未来语言的发展

**C++ Weekly - Ep 184 - What Are Higher Order Functions?** https://www.youtube.com/watch?v=NoIJoL3cIJ4

- 这一期比较简单，就介绍 higher order functiongh ord

**C++ Weekly - Ep 183 - Start Using Raw String Literals** https://www.youtube.com/watch?v=DiZ-az_nJMM

- raw string literal

**C++ Weekly - Ep 182 - Overloading In C and C++** https://www.youtube.com/watch?v=EqPlVZLEHlA

- 介绍的 C 的 `_Generic`

**C++ Weekly - Ep 181 - Fixing Our bind_front with std::invoke** https://www.youtube.com/watch?v=s2Kqcn5e73c

- 用 lambda 实现的简易版 bind_front 没法支持 member function 的bind，解法是直接利用 std::invoke；其实这里揭示了什么时候需要 std::invoke
- 人肉版本的 std::invoke 实在太 tedious 了

**C++ Weekly - Ep 180 - Whitespace Is Meaningless** https://www.youtube.com/watch?v=JqhbzY04VlE

- C++源代码中绝大多数情况空格不敏感，所以大胆放心用 clang-format（大意如此）

**静态链接和静态库实践指北-原理篇** https://zhuanlan.zhihu.com/p/595527528

- 讲道理有点一般，不过对于没啥经验的人来说算是个实践性比较好的文章

**C++ 异常是如何实现的** https://zhuanlan.zhihu.com/p/406894769

- 其实就是 **How do exceptions work in C++ on Linux?** https://pvs-studio.com/en/blog/posts/cpp/1333/ 这篇的一个综述

**How to Use Compiler Sanitizers in Your Conan C/C++ Workflow** https://blog.conan.io/sanitizers/toolchain/tools/conan/2025/11/25/How-to-use-sanitizers-in-your-conan-workflow.html

- Conan 官方的文章
- 首先 conan 在 profile 层面支持 sanitizers

    ```toml
    # gcc_asan

    [settings]
    arch=x86_64
    os=Linux
    build_type=Debug
    compiler=gcc
    compiler.cppstd=gnu20
    compiler.libcxx=libstdc++11
    compiler.version=15
    compiler.sanitizer=Address

    [conf]
    tools.build:cflags+=["-fsanitize=address", "-fno-omit-frame-pointer"]
    tools.build:cxxflags+=["-fsanitize=address", "-fno-omit-frame-pointer"]
    tools.build:exelinkflags+=["-fsanitize=address"]
    tools.build:sharedlinkflags+=["-fsanitize=address"]

    [runenv]
    ASAN_OPTIONS=halt_on_error=1:detect_leaks=1
    ```

- 构建依赖的时候使用 sanitizer profile

    ```bash
    conan install . -pr=gcc_asan -b=missing
    cmake --preset conan-debug -DCMAKE_VERBOSE_MAKEFILE=ON
    ```

\#2

这一周闲暇时间主要在研究 conan 构建包，主要是我们的使用场景和公司的基建不一致导致的。

核心动力是希望给 Scheduler 做构建系统的升级，而换掉构建系统，换掉 httpd 又是很重要的一步，而换掉 httpd 换上 fawkes（yes，实现 fawkes 的 motivation 就是为了解决我们那坨屎山）又需要先升级工具链，which 意味着要从 alma-8 升级到 alma-9。

之前我们三方依赖包都是自己（利用公司Jenkins）构建并打成 RPM，实践下来非常难用，而且也没有对构建系统的支持，例如 pkgconfig/cmake 这些，不过这个主要是我们打包的流程过于野生。

考虑到后面是一定要引入包管理的，所以不如提前引入，把3方依赖转移到conan上，然后再做系统迁移。

但是遇到另外一个问题，我们的 toolchain（gcc-10/c++17）在公司的 conan artifactory 上根本没有对应的 pre-built binary，所以意味着几乎每个包都要我们重新编译。

虽然理论上可以不用 pre-built 而直接走现场编译，但是 CI 上跑这个几乎没法有效利用 cache，而且 conan 的 cache 不是并发安全的。

综合下来稳妥的方案就是：我们为每个依赖都提前创建一份我们 toolchain 构建的 binary。

公司 build team 提供的方案我瞄了一眼：手动构建依赖包 + conan export-pkg

我觉得这个方案合理，但是不够好，因为大部分依赖都有现成的 conanfile.py recipe，直接复用就行了（不过实践下来确实有点点坑）；另外就算要自己写 recipe，构建的流程也应该直接写 conanfile.py 里。

所以我决定按照自己的思路来跑。

目前验了 gtest 和 abseil-cpp 确认是可行的并且最大长度的复用了官方的 recipe，好处之一就是官方的 recipe 写的好多了，一些参数选项全都通过 options 暴露出来，只需要在 `conan build` 时指定就行。

不过也确实踩了一些坑：

- 首先是要能看懂官方的 conanfile.py，这个确实有点门槛，我也是先花了一天时间翻完了官方 tutorial 有了大致印象，再依靠两位G老师才解决了一些问题。例如 `source()` 函数和 `exports_source` attribute 的使用场景 .etc
- conan 对 dependent package info 是更加严格的，所以一些依赖问题单纯跑 cmake build/install 不会触发，但是再 conan 里 package 就会出现；这里点名 abseil-cpp-202601 的版本。conan 给他做了一个 Patch，刚好利用官方的 Recipe 应用上即可

不过下周最大的问题是得和公司的 build team 打交道，把相关权限和构建 setup 起来。而且这个还得偷偷的搞，一些涉及 ticket 的还要让 chao 那边出人来搞，不然单子直接到老崔那边了...

---

好了这周就这样，下周见
