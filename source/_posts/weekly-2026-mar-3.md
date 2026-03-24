---
title: 一周杂记 in Week 3 Mar 2026
categories: CODE-LIFE
date: 2026-03-24 00:35:59
tags: [杂记]
---
本周（03/16 ~ 03/22）是3月份的第三周，这周空气不是很好。

## Life

\#1

这周 HRV 还是一如既往烂，虽然 RHR 在50~54之间徘徊，但是运动量是真的减少了。

只有周日醒来发现短暂回升到了67，周日一连冲击，周一醒来发现又懵掉到50多，确实有点离谱

感觉还是有什么无形压力刺激 🤷‍♂️

\#2

周日醒来感觉还挺好，十点多出发带着老婆去市中心的万豪参加美敦力主持的起搏器电生理有关的会议。

当然我和我媳妇儿主要还是去吃饭的...

别人8点开始开会，我们十点半多才入场，然后听了一个小时又提前溜了就去酒店饭堂吃饭了。

不过现在万豪堕落到中午的自助餐是真的烂，刺身连北极贝帝王蟹都没了，牛排也煎过头了。

老婆吃了一点都不知道剩下的选啥，绕了半天搞了一碗炒面...

吃完就直接回家了，回家还到头睡了一个多小时 🤔

\#3

这周没有看电影也没打游戏。

现在鸭科夫全成就了我都不知道接下来打啥，《师父》不知道会不会太花时间

## Work

\#1

**CppCon 2023 | Back to Basics: Forwarding References - How to Forward Parameters in Modern C++ - Mateusz Pusz** https://www.youtube.com/watch?v=0GXnfi9RAlU&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=29

- 非常推荐的教学 talk，中后段还分享了非常实用的 tricks
    - 如何解决单参 forwarding references 干扰 copy/move ctor
    - 如何 enforce 一个 forwarding reference 只能用作 rvalue reference
    - 涉及 coroutine 的 rvalue 避免变成 rvalue reference

**C++ Weekly - Ep 523 - Why I'm Still Using std::cout (on this channel)** https://www.youtube.com/watch?v=TreruByxQWE

- `std::print()` 生成的代码太多了，甚至不如额外添加 fmtlib 用 `fmt::print()`
- 所以用 `std::cout` 的原因就是方便而且生成的汇编简单很多

**C++ Weekly - Ep 175 - Spaceships in C++20: operator 〈=〉** https://www.youtube.com/watch?v=0SeUPb8LC9I

- 介绍 c++ 20 的 `operator <=>` 这个超级好用
- 如果不能用碰到 members 很多并且比较 trivial 的场景，直接用 `std::tie()` 偷懒也行

**C++ Weekly - Ep 174 - C++20's `std::bind_front`**https://www.youtube.com/watch?v=7pqPfQ_HpmI

- 比 std::bind() 又好用又直观

**C++ Weekly - Ep 173 - The Important Parts of C++98 in 13 Minutes** https://www.youtube.com/watch?v=78Y_LRZPVRg

- 这是真考古了

**C++ Weekly - Ep 172 - Execution Support in Compiler Explorer** https://www.youtube.com/watch?v=4h8IOiu-K1c

- 原来指的是允许执行代码片段啊。。。第一眼还以为是 cpp std 那个 execution support. 囧rz

**Mixed C++ Monorepo Project Structure Development and Build Workflow** https://galowicz.de/2023/01/23/mixed-cpp-monorepo-project/

- 说是提供一个 C++ monorepo 的工程目录的设计参考，但是我觉得完全没啥内容啊…

\#2

这周花了不少时间给 chao 打黑工，把我们的依赖从自己打的 rpm 迁移到 conan 上去。

中间踩了少坑，不过目前也算顺利，已经迁移了接近 30% 的包，而且 core/util 这个核心目录构建下来也没有问题

下周会研究一下 vendors 没有的包的导入以及继续迁新的包

\#3

这周断断续续用 cursor 搭配 opus 把 cmake fmt 写完了，微调之后完美满足需求。

有顺带整了一下 pre-commit hook 的输出，让他弄的好看一点。

整着一块的改动都在这了：https://github.com/kingsamchen/fawkes/pull/25

---

好了这周就这样，下周见
