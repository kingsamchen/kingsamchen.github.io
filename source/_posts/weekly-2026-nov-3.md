---
title: 一周杂记 in Week 3 Nov 2026
categories: CODE-LIFE
date: 2026-11-23 23:54:45
tags: [杂记]
---
本周（11.16 ~ 11.22）是11月份第三周，天气上已经进入了冬天的节奏，而且这周坑事儿太多。

## Life

\#1

这周出了周一部署的问题外，整体上还算风平浪静，周四上午搞了一个联合另外一个组的 all hands，因为我们都在同一个大 head/mgr 下面。

这个 all hands 有点作用吧，但是不多，主要还是确立一下产品线目标和做事风格一类的

不过我不觉得老崔会有什么行事上的变化。

\#2

周五居家本来就要迎接完美的周末的，媳妇儿下午睡醒之后非想去吃朱富贵。

我说我差不多17点才能提前下班，因为确实有一些事情，而且今天周五，这个点过去我觉得不太来得及

但是媳妇儿抱着试一试的心态，我们还是出发了。

结果刚开出去没多久就因为路段有个学校给堵傻了，不过这个其实也不算问题，因为刚坐上车，就提示前方排队80多桌...

到了万达之后因为停车的问题又耽搁了一阵，但是对于前面80多桌实在没多少实际影响

因为我路上一直在抱怨吐槽，所以我们在朱富贵那层等了十几分钟之后媳妇儿就说不想吃了她把排号取消了。更坑的是我直接找不到她人，因为我上5F影院躺椅休息了

微信/电话都不接，于是我很生气的就自己开车回家了，回家还只能吃减脂鸡胸肉...

她自己一直到晚上接近十点才到家；因为这个周六一天两个人都没啥好话说

还好周日基本情绪缓和。

有一说一，这是第二次说要吃朱富贵结果排号太多不相等不欢而散了

\#3

这周六拳击课练腿比较多，也做了劈叉拉伸，舒展髋关节，确实有点难

不过最近感觉体能下降了，不知道是不是和我买的能量饮料是低糖的有关系。。。

下周感觉得换成以前那款能量饮料了，而且感觉平时心肺得重新开始训练了 😅

## Work

\#1

**CppCon 2023 | "Distributed Ranges": Model for Building Distributed Data Structures, Algorithms & Views - Ben Brock** https://www.youtube.com/watch?v=X_dlJcV21YI&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=83

- 核心是，利用 RDMA 把远程机器的内存访问模式构建出来之后，在这个基础之上，构建一套 distributed range 然后搭配 ranges views
- 底下可以跑 SYCL 或者普通的 MPI

**CppCon 2023 | Noexcept? Enabling Testing of Contract Checks in C++ - Pablo Halpern & Timur Doumler** https://www.youtube.com/watch?v=BS3Nr2I32XQ&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=87

- 这个分享非常有意思，核心分两部分：
- 首先两个作者都推荐使用 contracts 语义，不一定要是特性，直接用 assert 或者自己定义的 ASSERT 都可以；precondition 和 postcondition 都有的是 narror contract 只有一个是 wide
- contracts 很重要但是本身也需要测试，搞来搞去发现最合适的其实是违反时抛个异常，这样ut 写起来很方便；但是有个坑点是，跑CI的时候会专门覆盖到 contracts violations 但是最终部署的版本不会，所以最终版本会把对应的 contracts 关闭；然而 noexcept 这个是没法关掉的，所以如果函数本身不会抛异常，语义上也不会，标了 noexcept 之后你在 ci 环境想验证 contracts violation 就会直接 terminate
- 换言之，noexcept 对 contracts coverage 是不兼容的；把 noexcept 也做成 conditional 宏不是一个好实践，很多人都趟过浑水，认为是一个不良实践，因为 macroed noexcept 这个属于悄然改变语义
- 所以就来到下半部分核心：minimize the use of noexcept，只给 default ctor / move-ctor/assignment 等 move-like 函数使用；以及非常 trivial 的 getters

**CppCon 2023 | Lightning Talk: Interfaces in C++ - Megh Parikh** https://www.youtube.com/watch?v=zPudH_y4OXo&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=89

- 大概是说 naive concept 的约束比起 virtual functions 那套的约束不够，然后演示了自己加了一堆东西
- 但是是话说我觉得 sample 不太行

**CppCon 2023 | Lightning Talk: Whitespace: A Humorous Short Talk - Dan Curran** https://www.youtube.com/watch?v=Kg3rw3KJbMk&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=90

- 讲 code format 的，Speaker 喜欢函数参数多的时候单个一行，然后放到左边去

\#2

周一产线刚部署没多久 oncall 的同事 pager duty 就告警了，囧rz

因为我的 api-v2 第一次上正是产线，所以有点紧张，还好经过研究调查后和我的 api-v2 没关系，是早前杨大伯给 auth/jwt 埋了一个坑，chao 重构的时候踩到了。

因为定位到问题的时候已经傍晚五点多了，按照规定只能快速回滚并且因为错过了这周的 release window 所以只能等下周一重新部署

\#3

这周周四周五乃至周末有一大半时间都在研究怎么合情合理的把 request-id 传递到 fawkes layer 的日志里去

因为业务层绑定之前的 Context 用的 RequestState 语义过于丰富，fawkes 这一层作为基建层，不可能直接依赖高层抽象的。

但是即使我搞出一个 `OperationContext` 放到基础层，这样 storage layer / api business layer 和 infra layer 都可以依赖它，但是还面临一个问题：fawkes 还有开源版实现，如果不想两个版本差异太大，`OperationContext` 对 fawkes 这一层的浸染应当尽可能少

目前初步有一个想法，但是还需要带入到 fawkes 具体验证后才能确定可行，这个只能等下周了

---

好了这周就这样，下周见

