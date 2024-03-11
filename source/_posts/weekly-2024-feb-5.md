---
title: 一周杂记 in Week 5 Feb 2024
categories: CODE-LIFE
date: 2024-03-04 23:50:31
tags: [杂记]
---
本周（02/26 ~ 03/03）是2月份最后一周；因为本周2月天数略大于3月，所以归入2月

## Life

\#1

这周是复工的第二周，但是感觉工作上的一些负面是想影响到了我的情绪和心态，不仅导致每天上班后的心情变得比较诡异，非工作时间的学习状态也变得不是很对头。

明显感觉这周学习进度不太理想，姑且还是暂时算作我还在假期综合症期间吧。

\#2

极氪新款001上市后就决定试驾购买，恰好老蔡有个小弟在离我家很近的店做🥇销售，所以比较顺利地搭上了话，并且约了周日早上9:30的试驾。

和媳妇儿楼下小笼包店吃完早饭到体验店的时候其实已经9:40了，刚好遇到了店里有辆车被副店长开到了湖州给顾客上门试驾...

吐槽一下这个副店长做事不靠谱，大白天的开车百来公里跑去给自己的客户试驾这也太离谱了

在店里喝了杯咖啡一直等到快11点才终于坐上试驾车，一次体验了一把正常行驶的空悬调整，弹射起步和急速加速，还有极限变道和极限转弯（这一把是老蔡小弟操作的，我的水平显然还不好做这个）

那个感觉只有一个爽字；并且第一次弹射之后我媳妇儿坐在老板位上也默默寄上了安全带。

安全小贴士💡：坐后排也应当系安全带哦

轮到我自己开的时候感觉只有一个字：稳。不知不觉开到快 80kmh 都稳得和2、30kmh 一样，而且那个极限加速是真的爽飞

试驾结束后就下定了，考虑到绿色还没亲眼看，所以先定了媳妇儿中意的灰黑以及搭配的内饰，ME版

约了周二上午去工厂里看一下绿色在做最后的锁单提交

\#3

本周观影

- **首尔之春 5/5** 政治没有公平正义，只有赢家输家
- **盗火线 Heat 5/5** 这风格感觉至少影响了一个时代同类型电影，看帕西诺和德尼罗在荧幕做对手可不是年年有；那年的方基莫还略显青涩
- **代号47 Hitman: Agent 47 3/5** 剧本完成度不太行，但是开场前十五分钟的枪战和动作戏合我胃口。Spock 你个浓眉大眼的也跳反了啊

## Work

\#1

本周学习进度

**Design Patterns in Modern C++ | 6. Adapter**

- 用了一个很生硬的例子讲了 adapter pattern…

**Design Patterns in Modern C++ | 7. Bridge**

- 说 pimpl 就是 bridge pattern 的应用…看完我还是不懂 key point…

**CppCon 2021 | C++ Standards Committee - Fireside Chat Panel**

- 和委员会委员们面对面Q&A + 说话的艺术…
- 感觉有点像和领导one-one，听了很多很有道理的话但是最后实际上对结果没有什么影响…

**CppCon 2021 | Design Patterns: Facts and Misconceptions - Klaus Iglberger**

- 先“吐槽”了一下如今 std::make_unique 除了语义部分之外重要性大大下降；并且指出 make_unique 并不是一个 design pattern
- 然后讲 design pattern 的意义是针对那些为人熟知的问题引入一些贴身的抽象，并且这些抽象的目的（intent）很重要
- 最后大篇幅阐明了 design pattern 并没有过时，并且也不针对 OO，并且C++标准库的实现也大量应用了 design patterns

**CppCon 2021 | Beyond struct: Meta-programming a struct Replacement in C++20 - John Bandela**

- 用 TMP 实现 meta_struct 定义类的同时提供类型信息，并且（在此基础上）实现静态反射
- 算是个 PoC，但是视频里没看到分享的 repo 地址

**std::index_sequence and its Improvement in C++20** https://www.fluentcpp.com/2021/03/05/stdindex_sequence-and-its-improvement-in-c20/

- 核心点就是 C++ 20 开始 lambda 支持模板参数了，可以直接利用 lambda 作 `std::index_sequence<I…>` 展开了
- 文章里体面提到的传统 for_each 处理 tuple 的做法以后可以不需要再单独写一个 for_each_impl 了

**C++ Weekly - Ep 262 - std::string's 11 Confusing Constructors** https://www.youtube.com/watch?v=3MOw1a9B7kc

- `std::string` 的重载坑
- 印象中 `std::string` 的重载设计问题是吐槽最多的

**Escape analysis hates copy elision** https://quuxplusone.github.io/blog/2021/03/07/copy-elision-borks-escape-analysis/

- 核心就是 copy elision 有时候会阻碍编译器的 escape analysis，导致编译器无法确认某个变量是否被泄漏到另外一个 scope 从而选择保守的策略
- 文章里的 sample 其实很重要的一点是 `S::make()` 是没有函数体的，这是模拟函数不会被内联，如果加上一个内联函数体，让编译器有更充分的上下文，最终生成的指令是完全一致的
- 从另一个层面上说这也是局部性原理的一个佐证

**c++ tip of week 216 inject singleton** https://github.com/tip-of-the-week/cpp/blob/master/tips/216.md

- 总结一下就是如果某个类要用某个 singleton，不要直接在内部使用，这样会直接显式依赖这个 singleton，可以通过 ctor 参数讲 singleton 传递进来，类内部存 singleton 的 reference
- 这样测试场景下可以用 mock 类直接注入替代；不过这里的注入还是通过手动完成的

\#2

继续复（重新学）习深度学习入门的第四章

研究了好久 numpy 的几个 api，看了几篇 posts 终于明白了 numpy array 的 axis 行为语义...

感觉回头得专门抽个时间吧 numpy 过一遍

\#3

本来想着研究一下 clangd 的 config file 但是发现全局配置无法解决工程 compile database 相对路径问题，遂放弃

然后鬼使神差想着优化一下 esl 的 clang-tidy checks，于是有了 https://github.com/kingsamchen/esl/pull/14

中间发现之前一直在 pre-commit hook 里使用的 `git diff --no-ext` 居然是错误的，估计是升级了 windows 上的 git 才出现的，ubuntu 22.04 可以确认这个命令不会出错…

正确的版本是 `git diff --no-ext-diff`

---

好了这周就这样，下周见
