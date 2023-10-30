---
title: 一周杂记 in Week 4 Oct 2023
categories: CODE-LIFE
date: 2023-10-30 22:27:10
tags: [杂记]
---
本周（10/23 ~ 10/29）是十月份的最后一周，因为 10/30 和 31 两天被我归入到 11 月第一周了。

## Life

\#1

难得的团建又来了，这次安排在了周三。并且因为 US 总部大发善心，每人预算追加了 500，所以最终算下来我们有人均 700 多的预算。

最后大家定的方案是去吃人均500的日料（月龙吟），然后在附近的钱江公园歇息。

不过是话说我觉得月龙吟除了吃到了一些像鱼子酱，海胆这种大渔吃不到的之外，综合味道上并没有比大渔强多少，但是人均价格翻了一番...

而且很多同事其实不太喜欢日料刺身，所以这次的店选择感觉是有点不太合理的。不过考虑到预算比较大，而且必须要在11月前报销完成，所以仓促一点也算合理。

这次吃完后没有参与德扑牌局，而是和几个同事沿着钱塘江边走了几段，感觉还行，就当消耗多余热量了 😅

不过比较蛋疼的事5点多坐同事车回公司的时候遇上下班高峰在路上堵了半个多小时...

\#2

这周继续准备科目一，每日刷刷科目一的模拟卷，为下周周一（10/30）的科一考试准备

\#3

本周就看了一部电影，还是自己抽时间看的

Crank 2: High voltage 3/5 之前郭达斯坦森怒火攻心的续作。整体有点 cult 倾向，算是无脑类型片。

## Work

\#1

本周学习

**CppCon 2021 | Design and Implementation of Highly Scalable Quantifiable Data Structures in C++**

- 考虑 entropy 的无锁队列；没有开放具体实现代码，听的云里雾里

**CppCon 2021 | Lightning Talk: Fun with Pointers to Members - Dominic Pöschko**

- 一个作者自己实现的 type descriptor https://github.com/dominicpoeschko/aglio/blob/master/src/aglio/type_descriptor.hpp

**CppCon 2021 | Value in a Procedural World - Lisa Lippincott**

- 说是讲 procedural programming，但是完全不知所云….

**CppCon 2021 | 3D Graphics for Dummies - Chris Ryan**

- 图形学入门 talk
- 不过不是我的菜…

**凤凰架构 | 7. 从类库到服务**

- 这部分讲的其实就是服务发现

**Building Microservices | 7. Build**

- CI/CD pipeline 和 multi-repo / monorepo 的选择
- 不过我个人经历上看更偏好 per-team monorepo

**HBase原理与实践 | 1. HBase 概述**

- 概念和基础架构综述

\#2

后面打算基于 C++20 和 asio 的 coroutine 稍微深度熟悉一下 CXX 这边的网络开发技术栈，万一过几年要用到呢

所以要把很早之前用来练手学习的 Eureka/learn-asio 的构建部分改改，毕竟那会儿很多 building setup 我自己都已经不用了。

所以花了一些时间把工程迁移到了新的 CMake(Presets) + VCPKG 作为包管理的方案，整体上还算丝滑，尤其 VSCode 对 presets 的支持远超我预期

这部分改动见 https://github.com/kingsamchen/Eureka/pull/16

\#3

这周也花了一些时间把 login passwords PoC 给弄完了，整体代码比较简单。

不过在过程中我把原来选用的 cryptopp 库换成了 botan-3，而 botan-3 虽然可以通过 vcpkg 安装，但是它自己只提供基于 pkg-config 的 export

本来以为这下得自己基于 find_library/find_path 封装一层了，不过探索了一番发现 Windows 上居然也可以直接通过 scoop 安装 pkg-config...

装完之后就可以在 CMake 里借由 pkg-config 来 `find_package()` 了

这部分的 PR 在 https://github.com/kingsamchen/Eureka/pull/14

\#4

顺带学习了一下 inline namespace

找到一篇不错的解释的 Post：https://stackoverflow.com/questions/11016220/what-are-inline-namespaces-for

\#5

给 anvil 做了一个更新，支持了 vcpkg 和 cmake presets 等

具体见 https://github.com/kingsamchen/anvil/pull/6

---

好了这周就这样，下周见
