---
title: Monthly Read Posts in Dec 2018
categories: PROGRAMMING
date: 2019-01-01 15:36:47
tags: [golang, algorithms, redis, code quality]
---
## Programming Languages

[init functions in Go](https://medium.com/golangspec/init-functions-in-go-eac191b3860a)

这篇 post 总结起来就是深入浅出。

---

[The importance of knowing STL algorithms](https://www.fluentcpp.com/2017/01/05/the-importance-of-knowing-stl-algorithms/)

能熟练运用 STL algorithms 是一个 experienced c++ programmer 的基本要求。

另外里面提到了使用 algorithms 的两个 pitfalls，其中一个是滥用 `for_each()`，这个确实有点意思。
<!-- more -->
`for_each()` 是用来**产生副作用**这点确实很多人会忽略。

---

[Know your algorithms: algos on sets](https://www.fluentcpp.com/2017/01/09/know-your-algorithms-algos-on-sets/)

[How to (std::)find something efficiently with the STL](https://www.fluentcpp.com/2017/01/16/how-to-stdfind-something-efficiently-with-the-stl/)

[Searching when you have access to an STL container](https://www.fluentcpp.com/2017/01/26/searching-an-stl-container/)

[STL Function objects: Stateless is Stressless](https://www.fluentcpp.com/2017/01/23/stl-function-objects-stateless-is-stressless/)

STL 算法实践

---

[Ranges: the STL to the Next Level](https://www.fluentcpp.com/2017/01/12/ranges-stl-to-the-next-level/)

A quick introduction to upcoming range concept.

---

[Java vs C++: Trading UB for Semantic Memory Leaks (Same Problem, Different Punishment for Failure)](http://ithare.com/java-vs-c-trading-ub-for-semantic-memory-leaks-same-problem-different-punishment-for-failure/)

syntactic memory leak: objects which are unreachable but still present in the program

semantic memory leak: objects which are useless but still reachable

While all unreachable objects are useless, not all useless objects are unreachable.

作者文章里提到的 Java 这种带 GC 的语言很容易落入 semantic memory leak 的观点我是认同的，但是对于他的做法（nullify）我是不大认同的。

我猜可能作者在用 Java 去做一些游戏开发的过程中遇到了一些问题...

---

[Go 内置库第一季：time](https://cloud.tencent.com/developer/article/1371390)

[Go 内置库第一季：json](https://cloud.tencent.com/developer/article/1369649)

[Go 内置库第一季：reflect](https://cloud.tencent.com/developer/article/1368789)

[Go 内置库第一季：net/url](https://cloud.tencent.com/developer/article/1365984)

[Go 内置库第一季：strings](https://cloud.tencent.com/developer/article/1368086)

golang 标准库入门扫盲。

---

[Context API explained](https://siadat.github.io/post/context)

[Context-Based Programming in Go](https://code.tutsplus.com/tutorials/context-based-programming-in-go--cms-29290)

覆盖了 golang 里 context 的基本使用。

---

[Uses of Inheritance](https://arne-mertz.de/2015/02/uses-of-inheritance/)

覆盖了部份非常规的 inheritance 用法。


## Database

[Redis 极简上手教程](https://dev.to/luispcosta/redis---an-introduction-3lof)

如其名，非常简约，差不多十几二十分钟就能看完。

文章里特意首先提了几点使用 redis 的经验法则：
- namespace your store
- enforce a TTL for a cached result (cache lifetime control strategy)
- don't abuse/overuse redis
- be careful about your operations if your are working with huge datasets


## Software Engineering

[Too Much Unit Testing Is Detrimental for Code Quality?](http://ithare.com/too-much-unit-testing-is-detrimental-for-code-quality/)

作者针对一些大型开源项目，选取了几个 metrics 之后结合单元测试分布进行分析，最后结果表明，单元测试覆盖大概在10%-20%的时候，这几个 metrics 的综合指标最好。

由此得出，（静态类型语言工程中）单元测试不是越多越好，20%是一个比较理想的数字。

但是有一点需要注意，选取的指标不一定能反映所谓的 code quality，正所谓 correlation is not causation。

然而作者的 idea 倒是具有一些参考意义。

---

[C++:“model of the hardware” vs “model of the compiler”](http://ithare.com/c-model-of-the-hardware-vs-model-of-the-compiler/)

Why standard of C++ shouldn't bring into concept of platforms/compilers.

## Network Programming

[如何设计与实现一个自定义的二进制协议](https://zhuanlan.zhihu.com/p/21999964)

observations:
- 如何消息分帧
- 如何做应用层心跳
