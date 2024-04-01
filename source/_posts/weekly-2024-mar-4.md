---
title: 一周杂记 in Week 4 Mar 2024
categories: CODE-LIFE
date: 2024-04-01 20:14:35
tags: [杂记]
---
本周（03/25 ~ 03/31）是三月份最后一周，下周一就是4月一号了

## Life

\#1

周五媳妇儿二战雅思，本来我决定都报班了而且“认真”复习了一个月应该没问题了吧。

结果傍晚5点考完就和我说这次考得很差，然后连晚饭都不回来吃了。

8点到家的时候直接哭了一次....

我说算了算了不考了，大不了就不读博士不换其他医院了...

哎，真是难绷啊。一离开学校就放松学习现在就直接把路走窄了。

\#2

周五晚上极氪销售通知说车马上下线了可以交付尾款了。

因为想着薅一下信用卡积分所以约定好第二天去交付中心现场支付

于是第二天周六一早骑着电驴跑到交付中心，结果这简单的付尾款也弄得一波三折。

一开始是手机APP限额，后来刷实体卡发现信用卡当场刷的要第二天才能入帐还款，所以最后变成信用卡走10W，剩下19.4W直接刷储蓄卡；中间还遇到POS机没电...

询问了一下销售，基本4.3前后就能提车了，所以一些相关准备，比如家充，要开始做起来了。🤣

\#3

本周观影

- **哥斯拉大战金刚2：帝国崛起 Godzilla x Kong: The New Empire** 3/5 有那么一瞬间还以为自己在看猩球崛起...这一部哥斯拉的篇幅感觉少了好多，而且整体有点仓促感。另外大表哥怎么感觉这么油了
- **红雀 Red Sparrow** 3.5/5 剧本上不太有新奇，各角色表现也基本在线，就是有点邪门

\#4

周日和 Hogan 一起又上了一节乐刻的杠铃雕塑团课，但是因为周日早上没怎么吃碳水，导致团课中后段应该出现低血糖了，差点给我送走...

想想有点后怕，以后早上上高强度锻炼真得先保证足够碳水摄入才行。

## Work

\#1

本周学习

**Design Patterns in Modern C++ | 14. Command**

- 介绍 command pattern 和 CQS
- 例子还是有点刻意

**Design Patterns in Modern C++ | 15. Interpreter**

- 建立一个简单的 AST 树来求解四则运算，然后展示 boost.spirit 搭配 visitor pattern 实现一个简单的 dsl parser
- 感觉就是毫无内涵的浅浅过了一下…

**Design Patterns in Modern C++ | 16. Iterator**

- 用二叉树为例子简单讲了一下 C++ 范式下的 iterator，也不是很深入
- 顺带讲了一下 coroutine/generator 可以简化 iterator 设计

**Design Patterns in Modern C++ | 17. Mediator**

- 用 chat room 讲中介者模式还挺不错的就…
- 另外具体实现上可以用 observer pattern（书里用的 boost.signal）来做 message relay/broadcast

**Design Patterns in Modern C++ | 18. Memento**

- memento 保存某个时刻的状态，可以用来实现 undo/redo

**Design Patterns in Modern C++ | 19. Null Object**

- 核心就一句：A null object is a class which conforms to the interface but contains no functionality.
- 另外里面作为例子的 logger 拆分出外部操作的实体和内部真正用来 logging 的行为也可以学习一下

**CppCon 2021 | The Preprocessor: Everything You Need to Know and More! - Brian Ruth**

- 各种预处理宏的基础，干货很多

**CppCon 2021 | Bringing Existing Code to CUDA Using constexpr and std::pmr - Bowie Owens**

- 如何利用 polymorphic allocator 将一些容器的内存分配到 cuda 的专属内存中
- 这个领域完全不熟，基本当英语听力了…

**CppCon 2021 | Composable C++: Principles and Patterns - Ben Deane**

- 比较高层次的软件工程哲学了
- composability is not about syntax, its about semantics
- 讲了一些 类型、函数、对象甚至结构如何具有/形成 composability，以及composability 能够带来设计上的优势：能够应对（foresee）未来的需求

**Memory error checking in C and C++: Comparing Sanitizers and Valgrind** https://developers.redhat.com/blog/2021/05/05/memory-error-checking-in-c-and-c-comparing-sanitizers-and-valgrind

- RedHat 官方出品的算是非常详细的介绍 sanitizer 的 introduction
- 里面拿 valgrind 做了对比，表明目前 sanitizer 更具竞争力，缺点就是需要 recompile programs
- 还涉及了一些 options 的调优

**Concise Result Extraction in Modern C++** https://tech.davidgorski.ca/concise-result-extraction-in-modern-c/

- 这篇的核心就是怎么解决一次处理多个 `Result<T>` 的情况
- 但是他这路子走歪了…从错误处理的角度来说，需要适配多操作的话应该选择 monadic operations，比如未来标准中的 std::expected
- 或者干脆和 `absl::StatusOr<T>` 一样干脆不过多处理

**Scaling Shared Data in Distributed Systems** https://bravenewgeek.com/scaling-shared-data/

用多线程同步的思路启发分布式共享数据

- immutable data structures → 只读副本，如果场景合适，基本上是最好的选择
- last-writer win 策略解决多写冲突；因为 clock drift 存在所以 LWW 可能会出现写丢失
- 应用层定义的业务冲突解决方案
- 用 vector clock 等方法来实现 causal ordering，改进原始 LWW 中的 timestamp
- 使用 distributed data types(CRDT)

\#2

这周花了点时间清了一下 anvil 之前遗留的 issues，并且把 sanitizer 拆成了单独的 CMake module，也加上了 Windows 上 ASAN 的支持

完整的 PR 见：https://github.com/kingsamchen/anvil/pull/16

\#3

前面断断续续花了好几周的 revisit 神经网络基础部分结束了，可以继续后面的章节学习了。

感觉现在还是对 numpy 的了解少了一些，后面得专门抽个时间逐个核心文档的学过去

---

好了这周就这样，下周见
