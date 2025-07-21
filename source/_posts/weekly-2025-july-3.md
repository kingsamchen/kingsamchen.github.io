---
title: 一周杂记 in Week 3 July 2025
categories: CODE-LIFE
date: 2025-07-21 22:25:30
tags: [杂记]
---
本周（07/14 ~ 07/20）是七月份的第三周，这周天气还是暴晒+突然的暴雨，非常不舒服

## Life

\#1

这周是灾后重建第二周，也是一堆的事情，经常每天一堆会，周二周三早上还要8点起来和US那边开会，真有点顶不住。

重建事项中，我负责的“迁移到ZCP”和k8s环境对齐相当有一部分任务我们自己是做不了的，因为基本就是 SRE/DevOps 的活，所以只能摇来公司的在杭州这边的 infra sre team 来帮忙。

并且服务拆分也是一个很重要的前置任务，还好这块我和 suyang 一起合作起来还是比较顺利，这周已经能把最核心最重要目前最挣钱的 scheduler 服务单独拆除完成构建和运行时镜像打包。

下周目标除了 scheduler 服务试水 zcp 部署之外，其他 scheduler 的关联服务也可以参考我们现在做的做到拆分和 corp jenkins 的 docker 镜像构建

至于大集群的 setup 啊，网关迁移啊这些只能靠外援们来支持了 🤷‍♂️

\#2

周五先请几个同事一起吃了顿三分银，因为最近重建搞得实在太累了。而且因为还有 RCA 需要做报告弄得蔡司令情绪有点不对。

想了一下先找几个同事一起吃顿饭，稍微放松一下，回点血。

后面等 Roro 从美国来杭州的时候再一行人吃顿大的。

有一说一三分银确实挺辣的，吃完之后周末两天拉出来的屎都是金黄色的，还特别疼屁股。。。

\#3

这周末两天继续去媳妇儿家带娃，周六晚上还和媳妇儿抽空一起看了《长安的荔枝》

这周秋秋突然有点变了，喂奶的难度变高了，不像之前，饿了之后立马就喝奶；而是需要先好话哄一段，然后建立一些感情联系，才能让她喝奶。。。

不过最近她的睡眠质量倒是好了许多，经常白天倒头就睡，而且我特意问了媳妇儿现在爱睡不是因为有什么异样。

这个周日秋秋正式第60天啦

\#4

- **长安的荔枝** 3.5/5 大鹏有点过于倾向自己的舒适区了，把年会不能停那套搬过来只会显得这片不上不下不伦不类，两套叙事风格是不太兼容的。最高光的大概只有大棚自己的表演了
- **鹰眼 Eagle Eye** 4/5 当年好莱坞的剧情片是真的稳，情节推进丝丝入扣，而且没有明显的bug。说起来这片和 "I, Robot" 算是同类型电影，只不过这片相对更聚焦AI失控，看起来也更“实际”一些，而且对AI威胁的整体呈现比碟中谍最后两部是真的准确多了

## Work

\#1

**CppCon 2022 | 10 Years of Meeting C++ - Historical Highlights and the Future of C++ - Jens Weller** https://www.youtube.com/watch?v=Rbu_YP2sydo&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=53

- meeting c++ 这个会议和meetup 的多年发展历史以及一些重要 topic 的参与
- 可以当作听力练习

**CppCon 2022 | What Can Compiler Benchmarks Reveal About Metaprogramming Implementation Strategies Vincent Reverdy** https://www.youtube.com/watch?v=9bSG1aHXU60&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=73

- 自己实现的 metapack 和对应函数比起 STL tuple 的编译时间的差别
- 感觉对大多数人没啥用。。。

**CppCon 2022 | Lightning Talk: Cpp Change Detector Tests - Guy Bensky** https://www.youtube.com/watch?v=rv-zHn_Afko&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=155

- change detector tests 主要指那些覆盖的场景没有明确对错的，这些 tests 提供主要是为了做一个 acceptance 以及未来变更后引起注意的一些目的

**CppCon 2022 | Lightning Talk: Wisdom in Keywords - Jon Kalb** https://www.youtube.com/watch?v=MkH2hkFKy2w&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=162

- 形式有点意思，尽量不要用 reinterpret_cast 因为 constexpr 上下文没法用这个

**C++ Weekly - Ep 311 - ++i vs i++** https://www.youtube.com/watch?v=ObVRSNvGitE

- 老话题，不过这次用操作符重载来解释语义区别
- 如果仅用于自增 optimized build 下没有区别；不过 ++i 的仅自增的语义更加明确

**C++ Weekly - Ep 312 - Stop Using `constexpr` (And Use This Instead!)** https://www.youtube.com/watch?v=4pKtPWcl1Go

- 重点核心来看就一句：constexpr local variable is also a local variable，返回 constexpr local variable 的指针/引用仍然会导致 dangling access
- 所以才有 Jason 推荐的使用 static constexpr

**C++ Weekly - Ep 313 - The `constexpr` Problem That Took Me 5 Years To Fix!** https://www.youtube.com/watch?v=ABg4_EV5L3w

- 目的是搞一个 constexpr std::string，利用 array + string_view 互相倒腾
- 这一期黑魔法有点多了，尤其是那个 make_static 函数
- 哪天需要的时候可以回头来再看一下

**C++ Weekly - Ep 314 - Every Possible Way To Force The Compiler To Do Work At Compile-Time in C++** https://www.youtube.com/watch?v=UdwdJWQ5o78

- C++ 中如何强制保证 compile time eval，单纯的 constexpr function + constexpr variable 不能保证一定是在 compile-time 做的（??? Jason 说的是标准没有相关规定，但是实际中可以 assume）

**C++ Weekly - Ep 488 - 35 Years of Sad Game Dev Attempts** https://www.youtube.com/watch?v=4dyKlx1QmkU&t=17s

- Jason 这么多年的写代码历史

**现代化工具链在大规模 C++ 项目中的技术实践** https://mp.weixin.qq.com/s/dy3fHyOxIEPGULazojbsFQ

- 主要是 toolchain 方面的东西
- clang 有 thinLTO，代价小但是提升明显，不过 gcc 目前只有 FullLTO；剩下 AudoFDO 和 Bolt  这种 PGO 的要看公司基建支持；modules 的推广估计还要好几年

\#2

这周继续在写 fawkes 的 http server 部分，进度有点慢，因为平时上班没时间周末还要照顾娃，加上很多东西都要临时看 asio 和 beast 的 examples 临时学。

这周就突然想到一个问题：accept 连接之后的 socket 获取 remote_endpoint() 可能会抛出异常，但是跟了一下 asio 的源代码发现错误的情况除了提供的 fd 无效外，只有内部实际调用的 `::getpeername()` 调用失败，而运行时逻辑相关的错误只有对未连接的 sock 调用这个函数。

所以我想如果 accept 之后对 sock 调用前这个连接断开了，是不是会报错？但是第一感觉是不会，因为 socket 本身还是有效的。

然后问了一下 GPT 发现我的 reasoning 是对的，同时翻了一下以前老代码，发现以前处理链接错误，或者对端断开时候打日志时就是直接用 socket 拿 remote_endpoint。。。

所以这是时间一久又忘了 🫠

---

好了这周就这样，下周见
