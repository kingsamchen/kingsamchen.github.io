---
title: 一周杂记 in Week 3 Mar 2024
categories: CODE-LIFE
date: 2024-03-25 22:08:45
tags: [杂记]
---
本周（03/19 ~ 03/24）是三月份第三周

## Life

\#1

上周因为外公过世和媳妇儿一起回家奔丧，不知道是不是太过劳累亦或者那天恰好大降温着凉了，我和媳妇儿回家之后双双出现咳嗽鼻塞流涕的症状。

我自己的症状在周四和周五达到顶峰，每天咳痰咳个不停，有时候还会出现咳到干呕的情况。周五晚上在电影院几乎快咳出眼泪了...

媳妇儿之前在家备考我不在时就吃的非常随意，营养摄入不足，加上来回奔波而且周五还值了一个24小时的120，周末直接病情严重...

一开始她以为是新冠复阳或者甲流，但是在家测了试剂发现全都是阴性，所以只能解释为焦虑加上营养摄入不足导致免疫系统下降了

\#2

这周运动恢复状态一般，跑步基本只能坚持半小时就不行了

周日和 Hogan 在家附近的乐刻组了一次团课，虽然只有50分钟，但是强度几乎拉满，感觉比跑10公里还累

\#3

本周观影：

- **利益区域 The Zone of Interest** 2/5 如果只是想通过技术来表达自己建议再压缩1/3以节约我宝贵的时间。这种放弃叙事主攻技巧的影片差不多直接拍在我的厌恶点上
- **功夫熊猫4 Kung Fu Panda 4** 3/5 只能说这次比较敷衍，如果是带小朋友来看看也还行…
- **龙与地下城：侠盗荣耀 Dungeons & Dragons: Honor Among Thieves** 4/5 整体还不错，比较和我胃口，包袱笑料也都有，感觉是上佳的游戏改编作品

## Work

\#1

本周学习

**Design Patterns in Modern C++ | 13. Chain of Responsibility**

- 类似 Windows Hook Chain 那种处理机制
- 不过我不是很懂书里这一张非要不找重点的用 singly linked list 展示的目的是啥…

**价值投资实战手册 | 3. 企业分析案例**

- 这章主要就是作者自己挑选的企业分析案例讲解
- 但是实话说里面大部分企业所在的行业我都不了解所以看了半天也没学会门道
- 另，这本书完结了

**CppCon 2021 | C++ to the RISCue! A Practical Guide for Embedded C++20 - Kris Jusiak**

- 分享了作者自己实现一个 arduino 嵌入式设计的一些收获
- 重点是推销作者自己写的一个状态机库 boost-ext/sml，看着还算简洁用了不少TMP的魔法
- Immediately Invoked Function Expression 的作用，比如搭配 lambda 可以做到类似于 unroll 手动展开循环的效果
- ut & benmark .etc 快速略过了

**CppCon 2021 | Faster, Easier, Simpler Vectors - David Stone**

- 讲作者（这人也是委员会的）在自己实现一个 vector 中的心路历程…
- 讲的有宽泛而且不是针对教你如何去实现讲的，所以有点跳脱；最后部分讲了作者自己对于编码的一些思想关
- 但是感觉收获不多

**float division vs. multiplication speed** https://cppbenchmarks.wordpress.com/2020/11/10/float-division-vs-multiplication-speed/

- 只要编译器不把浮点数除法优化成对应的乘法，那么除法操作会比乘法慢 2~5 倍
- 使用 *`-freciprocal-math`* 可以以丢失精度为代价让编译器始终尝试进行除法改乘法优化

**C++ Fold Expressions 101** https://www.fluentcpp.com/2021/03/12/cpp-fold-expressions/

- 对于需要注意结合性（associative）的操作只需要记得结合性是从 `...` 往外结合
- 对于需要支持 0 参数的场景，可以使用 default value 来弥补，但是要注意这个 default value 要和 param pack 放一起，例如得是 `(0 + ... + values)` 而不是 `0 + ( ... + values)`

\#2

因为上周基本把 json serde for nlohmann 的原型做好了所以这周就没再继续 polish 这部分

相反突然想处理一下之前遇到的几个 anvil 上挂的 issue，以及顺带做一下 ASAN 的 Windows support

后者坑点还是不少的，尤其发现 sanitizer 到现在发展得特别快，有一些之前的认知已经不一定正确了，所以找了一些资料顺带加固一下影响

这俩部分应该下周都能做完

---

好了这周就这样，下周见
