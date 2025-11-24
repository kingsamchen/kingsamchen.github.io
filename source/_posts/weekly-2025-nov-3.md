---
title: 一周杂记 in Week 3 Nov 2025
categories: CODE-LIFE
date: 2025-11-24 22:47:10
tags: [杂记]
---
本周（11/17 ~ 11/23）是十一月第三周，这周气温变化比较大，依旧是中午回升，早晚冷。

## Life

\#1

因为这周气温早晚差异大，所以带娃难度直接上来了。

晚上睡觉的时候要保证娃的睡眠温度，包括床和房间；白天趁着太阳好的时候我妈带下楼转转，而且因为太阳比较大，所以中午气温一下就上来了，这个时候就容易出汗。

所以这周娃的奶量也是抖动得厉害，不太稳定

事实证明带娃真是个技术活

\#2

周五趁着 WFH 的间隙，一大早还没上班就把车开到距离家2-3KM的JD养车去洗车。

距离上次洗车过了差不多半年了，上次洗车时候娃都还没生呢...

这地点距离家还是有点点远，因为没法骑电驴过去（电驴放不进后备箱啊），所以来回两次都选的共享单车，单程差不多要十几分钟吧，还好没有骑得一身汗。

不过洗的效果还是可以的，毕竟花了108块钱走了一个精洗。

\#3

周四晚上回到家之后先研究了一下终于成功把 Aeron 塞进了车后备箱，然后就去跑了一个步

跑完之后吃了饭洗个澡，趁着车流低峰以及办公室还没锁门，赶紧把车开到公司把椅子放到了办公室里。

一气呵成，最后回家的时候都还没到停车费结算时间 🤣

\#4

这周日终于把三个房间做了一个轮换，我现在又睡到了主卧，我妈谁次二卧，娃和老婆睡次一卧。

因为上上周买的大婴儿床有点大，没法推进出卧室所以只能选好卧室再拼接组装。

老婆看上了次一卧光照，通风所以直接把床在次一卧拼接，这次时间差不多了就做了房间调整。

\#5

上周小蛋被害之后没几天，救助的阿姨说又有1-2只橘猫被害了，而且我们小区公租房那幢楼有个神经病会杀猫，其他剩下的小猫现在都非常警惕，已经开始怕陌生人了。

最近几天放粮的时候确实一直身影都没看见，包括皮蛋和汤包也都不知道跑哪去了。

哎，现在这个环境实在是有够糟糕的。

## Work

\#1

**CppCon 2023 | Undefined Behavior in C++: What Every Programmer Should Know and Fear - Fedor Pikus** https://www.youtube.com/watch?v=k9N8OrhrSZw&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=12

- 感觉没有很新颖，还是 UB 可能导致的一些编译期的“优化”
- 不过这次区分Undefined 和 Unspecified 还是有点意思，后者是结果可穷尽，但是不确定是哪一种，前者完全不可预料，是整个程序 ill formed

**CppNow 2025 | Beyond Sequential Consistency - Leveraging Atomics for Fun & Profit - Christopher Fretz** https://www.youtube.com/watch?v=qNs0_kKpcIA&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=33

- X86-64 上 load/store 本身就是带有 acquire/release semantics；ARM 这种芯片上会有明显区别
- X864-64上用了 full fence 会有明显的性能下降，这部分生成的汇编会有 LOCK prefix
- 演示了一下只存储 64 integer 的 spsc lockless queue，如果只在这个范畴上，实现确实简单；有个 broadcast queue 有点意思，有点像 kafka 的 consumer group 有各自的副本
- 用 `alignas` 和 hardware_interference_destructive 来避免 false sharing，以及 read/write index cache 极致提升有点不明觉厉
- 不过整体来说 lockless 确实有点禁术/黑魔法了

**C++ Weekly - Ep 506 - Zero Cost Function Binding** https://www.youtube.com/watch?v=9laCL5GixNk

- 利用 non-type template paramter 配合 lambda 做一个把编译期参数直接绑定到编译期的东西

**C++ Weekly - Ep 505 - C++26's CNTTP bind Functions** https://www.youtube.com/watch?v=gIyuvqJnhi0

- C++ 26 开始 `std::bind_front` / `std::bind_back` 可以直接通过 template argument 绑定函数，产生的代码质量更高

```cpp
auto madd = [](int a, int b, int c) { return a * b + c; };
auto mul_plus_seven_cpp26 = std::bind_back<madd>(7);
assert(mul_plus_seven_cpp26(4, 10) == 47);
```

**C++ Weekly - Ep 237 - Teach Yourself C++ in ∞ Days** https://www.youtube.com/watch?v=zUQz4LBBz7M

- Jason 自己曾经学C++的心路历程….

**C++ Weekly - Ep 236 - Creating Allocator-Aware Types** https://www.youtube.com/watch?v=2LAsqp7UrNs

- 如果你的 custom types 要存 pmr allocator 容器

**C++ Weekly - Ep 235 - PMR: Amazing, Fast, But, Not Quite Magic** https://www.youtube.com/watch?v=vXJ1dwJ9QkI

- 介绍 pmr allocator
- monotonic_buffer_resource 只会在最后一次释放资源，并且如果没有足够的资源，会使用 upstream resource，默认 upstream resource 是 new_delete_resource，所以如果 backing store 不够了会直接往 heap 上分配的 🤣

**CMake + Conan + VS Code** https://www.youtube.com/watch?v=kswbIXYBCpo

- 过于入门了

**Package Management in C++ with Conan for Beginners** https://gist.github.com/ForgottenUmbrella/0f32f6446b2948a3a5a99687b264910d

- 针对的是 conan1 已经有点过时了，可以不看

**Introduction to Conan 2 - The Best C++ Package Manager?** https://www.youtube.com/watch?v=U-_RbUqDSTc

- 过于入门了，不过这个视频倒是让我知道了 conan install 的时候可以通过 `-of` 来控制 dependencies bootstrap 的位置，这个目录因为包含了 conan 的 toolchain file 所以也是比较重要的

**Conan 2.0 | C++ Package Manager - A detailed introduction** https://www.youtube.com/watch?v=7sLeMVUo8Kg

- Conan 的 introduction 做的倒是挺好，但是呢用的 premake 也太蛋疼了。。。

\#2

这周已经开始一边研究 gin 的 cors middleware 一边给 fawkes 加对应的 cors 实现

一周过去断断续续相关的内容写了大半，预计下周周末前就能写完

\#3

这周抽了点时间整了一份比较标准的 clangd config file 并且在 linux/ubuntu 上验证 OK。

不过在 Windows 上验证的时候发现了一些连带 pre-commit hook 的问题，于是研究了一下之后顺带做了一个 fix

另外之前发现 vcpkg 升级到 2025.10 的 tag 之后 fmtlib 终于可以升级到 v12 了，这样一来 spdlog 的编译问题也解决了，之前为了 workaround overlay 也可以去掉了

所以这几个变更都放到了一个 PR 里：https://github.com/kingsamchen/fawkes/pull/12

\#4

fawkes/cors 实现的时候发现又需要 variant overloaded visit，于是索性在 esl 中做了一个，这样不用每次都手打了。 https://github.com/kingsamchen/esl/pull/26

---

这周就这样吧，下周见
