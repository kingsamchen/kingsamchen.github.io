---
title: 一周杂记 in Week 4 May 2026
categories: CODE-LIFE
date: 2026-06-01 22:30:51
tags: [杂记]
---
本周（5/25 ~ 5/31）是五月份第四周，也是最后一周。

## Life

\#1

因为6~7两个月轮到我妈照看秋宝，所以周五傍晚我妈坐高铁来杭州。

老婆刚好那个点下班让我顺带先去接她，结果好死不死碰到下班高峰...不过还好最后都比较即使接到了人。

到家后秋宝还是勉强认出了我妈，所以也没有那么畏生。

吃过晚饭后我开车送装岳母回长隆，顺带待会长龙的空调挡板

\#2

周日凌晨特意买了爱奇艺单场次看我厂和PSG的欧冠决赛

梦幻开局给我整的开心的不行，然后就下半场碰到送点绝平了😭

最后是真没想到常规平局，加时也平局，最后点球大战憾负，实在有点难受了，毕竟之前胜利是那么近在咫尺

不过这个赛季至少拿了英超冠军，也不算颗粒无收了，下赛季再进决赛冲一把

\#3

周日下午开车和媳妇儿去了西湖景区的郭庄，因为限号所以只能在边上找个停车场先停车，然后骑着共享单车1-2公里走到了郭庄。

不过在里面就最多逛了15分钟...所以园林景观确实对我和我媳妇儿没啥吸引力

所以就当作户外走路了，毕竟回走的时候是徒步走了2公里

## Work

\#1

**C++ Weekly - Ep 534 - What is C++11's piecewise_construct?** https://www.youtube.com/watch?v=XSUlr7yAClc

- std::piecewise_construct 核心作用是作为 tag type 将多个参数打包成一个 std::tuple 传递给 std::pair 实现 in-place construction
- 例如 `std::pair<int, std::string>` 想调用 std::string 的多参构造函数来做原地构造，例如 `std::string(10, 'a')` ，不用这个 tag 没法完成
- 除了 pair 之外还有其他类型也支持这个 tag
- 一个不太容易注意到的点是，因为 std::map / std::unordered_map 底下也是用的 std::pair 保存 entry，所以实际上可以结合 emplace + piecewise_construct 来做复杂构造的原地构造

    ```cpp
        std::unordered_map<int, std::string> m;

        auto [it, inserted] = m.emplace(
            std::piecewise_construct,
            std::forward_as_tuple(42),      // arguments for constructing the key: int{42}
            std::forward_as_tuple(10, 'a')  // arguments for constructing the value: std::string(10, 'a')
        );
    ```


**C++ Weekly - Ep 138 - Will It C++? MIPS Architecture (1985)** https://www.youtube.com/watch?v=2HRqtx859ks

- Jason 淘了一个二手的 router 类型的设备，CPU刚好是 MIPS
- 尝试了一下在上面跑 gcc 构建
- 就纯折腾

**C++ Weekly - Ep 137 - C++ Is Not An Object Oriented Language** https://www.youtube.com/watch?v=AUT201AXeJg

- 老生常谈，回顾一下 Scott Meyter 的 C++ 是一个语言联邦就行
- 尤其 modern c++ 引入越来越多的 FP 特性了

**C++ Weekly - Ep 136 - How `inline` Might Affect The Optimizer** https://www.youtube.com/watch?v=GldFtXZkgYo

- inline 会影响编译器计算函数的 cost，例如略微降低一点允许内联的cost阈值，不过并不是标了之后就一定会被内联，因为可能阈值降低后还是函数本身的cost还是高于阈值

**C++ Weekly - Ep 135 - {fmt} is Addictive! Using {fmt} and spdlog** https://www.youtube.com/watch?v=KeS1ehp9IiI

- 介绍 fmtlib 和 spdlog
- 谁能想到 fmt 后来进入标准了呢

**operator<=> for C++20入门篇** https://zhuanlan.zhihu.com/p/101004501

- 内容有些部分比较老了，比如成员包含容器需要手动提供 operator== 这些
- 最大的问题是不成体系，可以看我自己的总结好了

\#2

周四中午为了给 scheduler 切 jenkins 到 CMake + Conan，专门起了一个 vagrant vm 验证一下这部分功能没有破坏，然后就发现 vm 挂在了安装 conan deps 上，错误提示拉去 boost 的时候有 dependencies.yml 找不到

这个问题看得我一头雾水，这以前明明好的哇。

经过了两个半天和 Opus 4.7 的高强度研究，期间让 chao 给我合了 N 个 MR做验证，终于大概让我猜出问题在哪了：

1. 公司的 build team 给 promoted release vendor 的包只打包了 `conanfile.py` 和 `conanmanifest.txt` 其他文件全都忽略了，而且只有 release vendor artifacts 是这样；这个可以通过对比 artifactory 上 dev vendor 和 release vendor 确认
2. boost 有一个安装 modules 时需要验证 modules 需要的依赖关系的 dependencies.yml 没有打入 relase vendor 包
3. 如果同一个版本既有 release vendor 又有 dev vendor，我们公司的 conan center 一定会用 release vendor
4. local vm 拉取了 release vendor 的 boost pacakge，导致安装失败

而且这个设定是故意的，当然我不知道这个 build team 是怎么想的，反正我也没法改变他们。

所以最后的解决方案是把这些 extra files 的信息都 embed 进 conanfile.py，然后重新构建并 promote 一遍所有 packages，因为 recipe revision 变了。

及时解决这个问题后，周五下午给 scheduler team 做了一个 quick introduction to new build ecosystem 的 sharing session。

然后周末把 jenkins 切到了 CMake + Conan 并且写好了上手文档

所以这件事基本算是做成了一半了

至于另一半，就是下周要把部署用的 docker image 也做相应的调整，不过我怀疑这里可能还有坑。🤡

---

好了，这周就这样，下周见
