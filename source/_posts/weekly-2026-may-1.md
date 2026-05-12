---
title: 一周杂记 in Week 1 May 2026
categories: CODE-LIFE
date: 2026-05-11 23:27:25
tags: [杂记]
---
本周（05/04 ~ 05/10）是五月份第一周，已经开始结束假期开始重新上班了。

## Life

\#1

5号吃了早饭后就开始收拾行李，然后家里人开车送到我们那动车站，又坐上高铁往杭州开了。

高铁路上还算顺利，秋宝虽然偶又不安分，但是整体还算还行，车上也睡了一个多小时。

到了杭州东后，推着秋宝花了半小时才坐上地铁，还是比较费时费力的，不过还好一站地铁就到了老婆医院，开上车就可以直接回家了。

不过蛋疼的是，车再老婆医院停了4天4夜，整个车沾满了像果胶一样黏黏的颗粒状的不知道什么东西，到家后没办法只能约了第二天的JD洗车，索性第二天再休一天陪产假缓一下。

好在洗车也比较顺利，因为是第一个工作日我去的也比较早，几乎是第二辆车，所以10点多就洗完开回来了。

洗车师傅和我说那个是树胶，建议不要把车长时间停在树下 orz

洗车叠加优惠券花了99，感觉还不如当时直接停东站呢，多花一点钱省事又省力

\#2

周四周五回去上班的状态感觉还行，同事基本都回办公室了，所以也有话聊。

而且办公室味道也没有之前那么大了，总理买了一个专业设备，测下来甲醛和TVOC也都是安全水平了。

不过周六还是选择 WFH，毕竟一周一天的机会不能浪费。周六上午顺带早起去老婆医院抽了个血验个血脂。

很可惜，低密度蛋白还是比合理区间最高值高了不少，而甘油三酯依然很高，所以最后老婆给开了阿斯利康的瑞舒伐他汀，一天一粒，先吃7-10然后去医院复查肝功能再决定后续方案

\#3

- **飞驰人生3‎ (2026)** 3/5 韩寒拍车戏还是可以的 但是同样的套路玩三次是不行的 而且2已经把这个套路给弄到头了

## Work

\#1

**CppCon 2023 | Better Code: Contracts in C++ - Sean Parent & Dave Abrahams** https://www.youtube.com/watch?v=OWsepDEh5lQ&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=38

- 两个人讲 contract 相关的相声
- contracts 代表 local reasoning，precondition 和 postcondition 可以分别区分 client-side fault 还是 implement side fault；invaraint 是 contracts 的核心，如果是OO，那么 class invaraints 也很重要
- 推荐的设计思路是：strong precondition + weak postcondition
A **strong precondition** can make implementation simpler and safer. For example, `pop_back()` may require non-empty input instead of defining behavior for empty containers.

    A **weak postcondition** gives the implementation freedom. For example, `sort()` should promise sorted output, not the exact sequence of comparisons it performs.

- error 不是 bug，并且 exception spec 也是 contracts，而且 exception handling 的核心不是 catch exceptions，而是 preserve contracts under failure
- 类似原子性，contracts/guarantees 不具备组合性，即 两个 strong guarantee 的操作的组合不是 strong guarantee
- 另外很重要的一点：documentation 也可以作为 contracts

**C++ Weekly - Ep 531 - You Are Using AI Wrong** https://www.youtube.com/watch?v=kRZWuyM46QY

- Jason 认为使用 AI 编码的正确姿势：
    1. 明确的目标
    2. 可重复的结果（ut 一类的可以反复验证）
    3. sandboxing 以避免 agent 逃逸

**C++ Weekly - Ep 146 - C++20's std::to_address** https://www.youtube.com/watch?v=ka7yYnxV1l8

- https://en.cppreference.com/cpp/memory/to_address 主要用来获取 fancy pointer (i.e. pointer-like objects）的指针，并且考虑到，很多 fancy pointer 要么 &operator*() 实现不是预期的，要么可能是一个 null dereference，而 to_address 可以做到 get pointer without deref，所以在 generic code 中提供这个。

**C++ Weekly - Ep 145 - Semi-Automatic `constexpr` and `noexcept`** https://www.youtube.com/watch?v=1FAcPvb0ZjU

- 这一期比较早了，介绍的是 Visual Studio 的静态分析可以检查出某些函数可以加 constexpr

**C++ Weekly - Ep 144 - Pure Functions in C++** https://www.youtube.com/watch?v=8ZxGABHcu40

- gnu::pure / gnu::const 可以标记函数没有副作用
- 不过用不对容易高出大麻烦

**C++ Weekly - Ep 143 - GNU Function Attributes** https://www.youtube.com/watch?v=ub4bVs8ixko

- C++17 开始编译器需要接受哪怕不认识的attributes，不过我觉得最后还是得做成一个 macro

**Object initialization and binary sizes** https://www.sandordargo.com/blog/2023/01/18/object-initialization-and-binary-sizes

- 感觉没啥意思，global/static duration 的 (constexpr) array 这一类的分配多了会写入 data/rdata 区导致 binary 膨胀
- 未初始化的或者同意初始化为0的因为塞 .bss（文章里都没提到）不会占用实际的 ELF binary

**Falsehoods programmers believe about undefined behavior** https://pvs-studio.com/en/blog/posts/cpp/1024/

- 这一篇一般，讲的都是各种关于 UB 的错误言论
- 不过意义不大

\#2

这周开始给 scheduler 实验性的调整 vagrant flow 以及验证一下 tests 是否都OK，毕竟迁移到用 conan 的时候顺带升级了好多依赖的版本。

看了一下 release cadence table，预计下周产线部署之后就可以开始把 vm + jenkins 都切到新构建了，然后再把部署的 dockerfile 做个调整就可以再 dev 验证然后这波就算做完了。

顺利的话预计6月份 release 就可以用新的构建。

---

好了，这周就这样，下周见
