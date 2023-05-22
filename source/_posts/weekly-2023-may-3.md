---
title: 一周杂记 in Week 3 May 2023
categories: CODE-LIFE
date: 2023-05-22 21:25:56
tags: [杂记]
---
本周（05/15 ~ 05/21）是五月的第三周。

## Life

\#1

这周某天刷 twitter 的时候突然刷到业内知名技术专家左耳朵耗子（coolshell）因心梗去世的消息。

一开始还以为是恶作剧，但是看到多个大V都确认了消息之后才确定这是真事。

哎，心脑血管疾病看起来已经是程序员的头号杀手了。想起去年体检的时候血脂爆表，而且已经开始影响到了心电图，被我媳妇儿骂了一顿，并且勒令调整饮食习惯，加强运动。

劳逸结合千万不能过度工作，甚至锻炼也不能过度锻炼。活得久最重要。

\#2

周二和同事一起看了戈达尔的《随心所欲》 3/5

整体来说我个人并不喜欢这个电影的表现手法，总觉得很刻意；而且影片表达的主题现在看并不是什么特别突破的东西，所以一度看的我昏昏欲睡。

另外看完了银护的圣诞特辑 3/5

整体也是感觉一般，各种尬...比起银护3确实差不少。

## Work

\#1

本周 CppCon 2020 因为开始刷 lightning talks，所以量贼大，毕竟一个 talk 只有 5mins 上下。

**CppCon 2020 | Functional Error and Optional-value Handling with STX - Basit Ayantunde**

- 演讲者公司做嵌入式系统内部用的一个基础库 STX，这个 STX 提供的一些错误处理的方法，比如
    - abort 这类的替代 panic 系列，提供上下文信息
    - monadic Result / Optional .etc 差不多类似 std::expected 和支持 monad 操作的 std::optional
    - 以及一些错误处理的性能比较；这里我其实非常不赞同批评异常在 sad path 下的性能太差，因为异常作为错误处理汇报机制它本身是面向错误的case，实际产品中异常处理引起显著开销的那个错误发生频率下早就被打出业务告警了。

以下都是 5mins 左右的 lightning talks

**CppCon 2020 | Become A Game Developer In 5 Minutes Or Less - Mathieu Ropert**

- 作者分享了一些开发游戏中的趣事

**CppCon 2020 | C++ Community Surveys - Jens Weller**

- 社区的一些 C++ 调查

**CppCon 2020 | What's My Object? - Staffan Tjernström**

- 引出了 start_lifetime_as<T> 这个提案

**CppCon 2020 | What did I learn teaching C++ to beginners - Alex Voronov**

- 分享了一下C++教学的事情

**CppCon 2020 | C++20 Lambdas: Familiar Template Syntax - Ben Deane**

- 简单介绍了一下 C++ 20 lambda 支持通过类似模板参数语法引入参数类型名字

**CppCon 2020 | How We Used To Be - Ben Deane**

- 分享了这本书 [https://book.douban.com/subject/10605778/](https://book.douban.com/subject/10605778/)

**CppCon 2020 | C++ Dependency Management the Meson Way - Jussi Pakkanen**

- 将 meson 的 dependency managment，但是没看懂具体有什么优势

**CppCon 2020 | Drinking from the Fire Hose: Keeping up with the evolving landscape of C++ - Brian Ruth**

- C++ 发展越来越快内容越来越多，但是不代表 you need to know everything. 但是 get familiar with names 还是大有脾益的

另外还抽空看了

**现代C++白皮书 | 8. C++17：大海迷航**

- BS 挑了一些核心的点讲了一些自己的看法

**现代C++白皮书 | 9. C++ 20：方向之争**

- C++ 20 核心特性在提案/投票过程中的一些 anecdotes

C++ 20 高级编程第三章看了几页，因为看的内容不多所以就没有特别记录了。

\#2

因为之前 vm remake 过，所以这周抽了点时间重新搞了一个 study-abseil-cpp 的 docker 环境。

并且考虑到希望在这个 docker container 里做一些更“自由”的事情，所以重新基于 socat 给 vm 加了一个 proxy-agent 服务将请求转发到宿主机的梯子上。

这样依赖 docker container 就算用默认的 bridge newtork mode 也可以翻墙。

\#3

上周搞得 postfix 日志桥接踩了一个 flagfile 的坑；不过也好在我们有完善的测试，MR 回归阶段就跑出来问题了。

一顿研究后又用了另外一个 trick 给解决了...

希望下周这个改动能顺利合进去。

---

好了这周就这样，下周见！
