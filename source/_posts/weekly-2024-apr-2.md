---
title: 一周杂记 in Week 2 Apr 2024
categories: CODE-LIFE
date: 2024-04-15 20:58:56
tags: [杂记]
---
本周（04/08 ~ 04/14）是四月份的第二周。

## Life

\#1

上周提了车之后，这周突然想到找公司同事 Zack 一起练车。刚好他就住我楼上，比较方便。

这周期间练了三次，第一次绕着公司转了一圈，又跑到外面高架练了一下，结果因为导航有点慢了路口开错了最后花了不少时间才回到家。

第二次路线是媳妇儿医院，去程因为有个路口比较奇葩，绕了一次才到；然后回程开始没多久遇到一辆大货车倒车，然后等了一会儿不想等了想换道绕开他，结果变道忘了看后视镜，差一点点和车撞了。。。

对面后来还特意开上来看了我一眼，估计是觉得哪个傻逼瞎开...🤣

第三次先送媳妇儿去大关附近的乐抵港找她朋友，这段比较顺利，就是临时停车起步那会儿车比较多有点紧张。

后来陪着 Zack 去文三路一个营业厅，那边老城区是真的难，路口不好确定方向，停车更是痛苦。

结束之后又去了附近另外一个地方他约了咸鱼卖家收 NAS，这段还比较顺利。

就是回家进地库的时候走神了然后一紧张电门当刹车差点撞到地库横杆，还好自己反应及时并且脚一直是正刹车位，又立马给我停住了。

停好车之后检查了一下前盖和轮毂都没有事儿，算是运气好吧 🤣

总结下来就是我现在开车，空间感和微调还需要很大的进步空间

\#2

周三的时候 US 的 lead 和我说我们老板对我的MR数量不满意，说我MR偏少。

我：呵呵呵呵

反正最后的结论就是我还是刷几个吧

那行吧，既然这么想看数量我就刷起来

\#3

- **Ahsoka S1 3/5** 感官略鸡肋，anakin 的片段是个惊喜，但是除 ahsoka 和 huyang 外其他角色尤其 sabine wren 的戏份丝毫无感
- **德古拉元年 Dracula Untold** 3/5 整体观感一般，角色刻画主角这边还行，撸哥演这种角色好像都还可以。看结尾感觉当初是想搞成漫威DC那种超自然宇宙？大佬吸血鬼一句：let the games begin 就没下文了…
- **幕府将军 Shogun S01** 最近比较火的一个美剧，和媳妇儿一起看的，才看了几集

## Work

\#1

本周学习

**Design Patterns in Modern C++ | 24. Visitor**

- 介绍 classic visitor pattern，附带的配合模板+marker base 的 acyclic 有点意思，不过因为需要大面积使用 dynamic_cast 所以性能一般
- 捎带了一下 std::variant/std::visit 的版本，其实如果编译器支持的话，这个版本比较合理
- 至此全书结束

**CppCon 2021 | PGAS in C++: A Portable Abstraction for Distributed Data Structures - Benjamin Brock**

- RDMA的分布式数据结构，感觉过于专有领域了，基本 get 不到点

**CppCon 2021 | Back To Basics: The Special Member Functions - Klaus Iglberger**

- 对拷贝构造/赋值和移动构造/赋值这部分讲的不错

**CppCon 2021 | Interactive C++ for Data Science - Vassil Vassilev**

- 在 data science 的场景下用 Cling 来 prototyping ? 感兴趣的可以看一下
- 提了一嘴一个比较新的 python/cxx binding cppyy
- 以及利用 cling 作为 compiler as a service

**比较raft ，basic paxos以及multi-paxos** https://zhuanlan.zhihu.com/p/25664121

- 之前没有系统的学过 paxos 和 raft，但是这篇 note/随笔 我感觉在“比较”上也做得挺到位了
- 过了一遍之后先把能理解的记下来，以后没准还用得着

\#2

这周把拖了几天的 Introduction to Linear Algebra 1.1 的习题做了，顺带总结了一下一些 vector / linear combinations 相关的几何性质

感觉一旦开始学习数学，时间就变得特别不够用 🤣

深度学习入门新的一章也开始看了，回头等这张看完了再写总结

\#3

absl/status 的源码学习本周花在上面的时间好像也就一个番茄...

感觉最近事情太多了，时间确实有点不够用了。

---

好了这周就这样，下周见
