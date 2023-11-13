---
title: 一周杂记 in Week 2 Nov 2023
categories: CODE-LIFE
date: 2023-11-13 22:33:35
tags: [杂记]
---
本周（11/06 ~ 11/12）是 11 月第二周

## Life

\#1

这周四参加了公司的 _We Care_ 公益活动，跑去萧山钱阿姨流浪动物收留中心当了一回儿义工。

不过因为周四不凑巧下午刚好下大雨，所以我除了和同事捐助了几百的猫狗粮以及一些不要的衣物作为毛孩子们的过冬用具之外，没有在那边帮上啥忙。

不过还是如愿看到了小喵咪们，虽然他们都被关在笼子里也撸不到...一开始还以为可以帮猫仔们剪指甲梳理毛发啥的 🤣

\#2

这周观影如下

- **彗星来的那一夜 Coherence** 4/5 前面略无聊中间开始有意思结局直接高能
- **坚毅之旅 Sur l'Adamant** 2/5 差点没给我困死，你管这叫精神病人？神他妈各个都比我还健康....这撑死就是老年活动中心实录
- **忌日快乐2 Happy Death Day 2U** 4/5 延续1的节奏，甚至比1更精彩，但是必须搭配1食用 XD
- **大陆酒店 the continental** 疾速追杀的衍生剧，以 Winston Scott 为主角，刚开始看

\#3

这周继续两天各练了4小时车，略微渐入佳境的感觉

周日一大早7:10开始出门练，11:20到家吃了中饭都直接给我困死，下午感觉坐在 Aeron 上整个人都是摇摇晃晃的...

最后补了一个半小时的觉才感觉好得多...

## Work

\#1

本周学习如下：

**Building Microservices | 8. Deployment**

- 讲了 cloud native 时代的微服务部署的内容，涵盖的内容也都比较全，还在 K8S 上着墨不少

**凤凰架构 | 10. 可观测性**

- logging, tracing, and metrics

**凤凰架构 | 11. 虚拟化容器**

- docker 前世今生和 Kubernetes 以及目前围绕 k8s 的一些辅助生态

**HBase原理与实践 | 3. HBase 依赖服务**

- ZK 和 HDFS

**CppCon 2021 | Embracing PODs Safely Until They Die - Alisdair Meredith & Nina Ranns**

- C++ 20 开始 `is_pod<>`` 这个 type trait 就 deprecated 了，取而代之的是要具体确认是否满足 trivial & standard_layout
- 然后展开讲了好多 trivialty 的细节，比如 trivially copyable and trivially constructible；这个影响 member 在 union 中的 implicit lifetime

**CppCon 2021 | A Hash Map for Graph Processing Workloads & a Methodology for Transactional Data Structures**

- 一个作用于 persistent non-volatile （已经在天国的傲腾？）的 concurrent hash map
- 太高端了完全听不懂，就记得有个 clwb 和 sfence 的指令可以 flush cacheline 避免 crash 时丢数据

**CppCon 2021 | Handling a Family of Hardware Devices with a Single Implementation - Ben Saks**

- 如何利用 traits class 来抽象一组具有类似功能的类
- 这个手法也是我比较喜欢的一种，尤其比起传统基于虚函数的服用灵活性更好，耦合性更小；作者也提到了如果用虚函数实现的话，`get_count()` 这种函数返回参数就不好定义了，而基于 traits classes，就可以用 traits 提供的返回类型

**Weekly Notes #33 Programming languages | std::launder和std::start_lifetime_as有什么联系和区别？**

- 两个操作都和获得指向处在生命周期中的对象指针有关
- launder 是获取某个地址上已经构建过的对象指针，start_lifetime_as 是在某个 buffer 开始构建新对象并获得指针，注意和 placement new 不同，start_lifetime_as 使用场景是目标 buffer 已经有满足对象的数据，不能再覆盖，只需要构建对象开启生命周期即可
- launder C++17引入，start_lifetime_as 要到 C++ 23

\#2

继续抽了点时间研究了一下 ASIO coroutine 模式下如何实现 timeout control

\#3

周末看到 Github 自己出了一套字体 Monaspace，试了一下感觉比较喜欢 Neon 这个 Style，于是配置上了。

感觉不错 XD

---

好了这周就这样，下周见
