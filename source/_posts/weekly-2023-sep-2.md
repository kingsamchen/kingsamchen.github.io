---
title: 一周杂记 in Week 2 Sep 2023
categories: CODE-LIFE
date: 2023-09-19 23:26:18
tags: [杂记]
---
本周（09/11 ~ 09/17）是9月第二周，距离十一大长假还有两周。

## Life

\#1

周三上午醒来的时候右眼感觉有点肿胀不舒服，但是依然去上班了。下午因为实在有点不舒服所以早早下班让媳妇儿检查了一下。

媳妇儿说是有点感染，买了一点左氧氟沙星滴眼液，但是第二天起来除了右眼结了一大坨眼屎之外没有明显好转，所以请了半天假跑到媳妇儿医院，让媳妇儿给我挂个号找个靠谱的医生。

医生看了一下说是麦粒肿，那个滴眼液足够了，用后记得热敷，还是需要几天才能恢复的。

然后媳妇儿又给我开了一瓶激素滴眼液，混着用，说应该可以加速痊愈。将信将疑，按照说的滴了半天，果然到了周五就好的差不多了

媳妇儿说我高低得给她弄个锦旗挂科室里 🤣

\#2

周六下午陪媳妇儿和丈母娘吃了顿饭，然后回到媳妇儿家过了一晚上。

因为媳妇儿加就两张床，试探性和媳妇儿睡一张，但是半夜媳妇儿把我敲醒说呼噜声有点大，让我去沙发睡...

结果我发现一个 bug：入户的声控灯太灵敏了，几分钟就亮一下，当天又没戴眼罩，导致我在沙发上呆了快一个小时压根没睡着...

最后我回到卧室换了另外一头，媳妇儿把耳塞戴了紧一点，终于睡着了...

虽然第二天我们俩睡眠质量都不咋地，而且还得一大早早期去媳妇儿医院陪她查房；她查房期间我在一个值班室又睡了快俩小时，期间还梦见自己在玩流星蝴蝶剑第五关...

睡醒后就舒服多了，和媳妇儿一起去衣之家吃了许府牛然后回家了

\#3

周日家庭影院和媳妇儿一起看了2017版的《东方快车谋杀案》3.5/5

我没看过原著，感觉这电影还可以

另外《活死人黎明》看了一半因为一直没找到时间看完，所以估计要等到下周

## Work

\#1

**CppCon 2021 | IMD in C++20: EVE of a new Era - Joel Falcou & Denis Yaroshevskiy**

- 分享他们做的 SIMD 的库 eve

**CppCon 2021 | Lightning Talk: Love Code Reviews? Try Pair Programming Instead - Ankur Satle**

- 推销结对编程的，但是我觉得这玩意儿很鸡肋

**CppCon 2021 | Typescripten: Generating Type-safe JavaScript Bindings for emscripten - Sebastian Theophil**

- C++ 和 TS interop 的编译器 https://github.com/think-cell/typescripten

**CppCon 2021 | Lightning Talk: Required Field for Designated Initialization - Lesley Lai**

- 这个 short talk 有点意思：某个 struct 想用 CXX20 designated initialization 的时候，某几个字段会被默认zero-init or default-init，但是我们同时又希望这几个值能够被明确初始化为某个值

**CppCon 2021 | Lightning Talk: FlexClass - Classes With Dynamic Size For Everyone - Breno Guimarães**

- 作者需要一个小尺寸 control block 的 shared_ptr 于是自己实现了一个;实现核心基础是 https://github.com/brenoguim/flexclass 这个类来控制紧凑布局

---

本周是贯彻两本书一周各自50页的第一周，效果还行

**凤凰架构 | 3. 事务处理**

- 本地事务（简要涵盖了 ACID）+ 外部事务 + 分布式事务

**DDIA | 10. Batch Processing**

- 核心讲了mapreduce 范式

**Building Microservices | 3. Splitting the Monolith**

- 如何着手 monolithic system 拆分
- 核心：***Have a clear understanding of what you expect to achieve & incremental***
- 作者推荐拆的时候先选择容易的部分，一点点来，有助于建立信心和正面反馈

\#2

学到一个小知识

C++ 20 开始可以用 `explicit(false)` 来标记某个隐式转换的构造函数，再也不用一个一个 NOLINT 了

https://en.cppreference.com/w/cpp/language/explicit

\#3

这周因为比较休闲，没有怎么写代码 🤣

---

好了这周就这样，下周见
