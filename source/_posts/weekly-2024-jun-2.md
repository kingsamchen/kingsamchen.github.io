---
title: 一周杂记 in Week 2 June 2024
categories: CODE-LIFE
date: 2024-06-17 15:48:21
tags: [杂记]
---
本周（06/11 ~ 06/16）是六月份第二周

## Life

\#1

感谢端午假期，本周从周二开始只有四天工作日，过起来稍微快了一些

周三下班和媳妇儿去看了一下她之前发现的离家大概2-3公里一个小区的猫，起了个名字叫阿彪。

媳妇儿也是贼矛盾的一个人，其实也喜欢猫，但是就是不能养家里。得，再过几年去临安买大别野养老的时候再拐流浪猫吧

周四又踢了场球，还好没受伤，但是踢满了全场脚拇指还是有点不舒服；而且最近天气变闷热了，快两个小时感觉有点撑不住。

周五傍晚去媳妇儿医院接下班，市区得路是真得堵啊。。。媳妇儿趁丈母娘还在，周末在自己家住了一天，周六三个一起去媳妇儿家附近电影院看了《朱同》。媳妇儿说最后几十分钟她直接睡着了。

另外就是奥体附近的商场停车费是真的贵啊，两小时20块钱。。。

周日和教练上完团课，他说要不一起去看电影，他信用卡一张电影票能 9 块，我一想反正也没啥事儿就约好一起下午去西溪银泰看了《机器人之梦》

看完六点多一起吃了个 KFC 周日拼...

\#2

本周观影

- **朱同在三年级丢失了超能力** 3/5 整体观感就是不上不下，整个一四不像。经历过中二的小学生涯不代表对那段时期有特殊的感情，可能你拍高中生活能有更多的思考即使我是如此的厌恶高中。
- **机器人之梦 Robot Dreams‎** 3/5 很难评，至少没有什么特别能打动我的。要不把标题换成：别慌，就算分手了你和前任也能好好的？
- **狂飙** 观看中，目前给四星吧，几个主演演技确实给力

## Work

\#1

本周学习

**C++ Templates 2nd | 5. Tricky Basics**

- 这章讲的小点还是非常多的：
    - zero initialization(`{}`)
    - access to symbols from base class that depends on template argument (dependent type)
    - pass by reference won’t decay
    - …

**CppCon 2021 | Embracing (and also Destroying) Variant Types Safely - Andrei Alexandrescu**

- 前半部分将 variadic parameter packs 的 expansion 规则，后1/3部分讲 std::variant 最核心的析构实现的优化
- 近乎全程的干货，偶有一些段子，挺欢乐
- Andrei 的 talk 感觉基本都很值得看

**CppCon 2021 | Embracing `noexcept` Operators and Specifiers Safely - John Lakos**

- 这个 talk 也是干货十足，前半部分讲 noexcept operator 的使用（如何用来判断某个操作是否是 noexcept），后半部分开始讲目的
- 单纯从性能上来说，标记 noexcept 并没有多少帮助，它的作用主要用在 move operations 和 swap 上

**CppCon 2021 | Debugging Assembly Language and GPU Kernels in Visual Studio Code - Julia Reid**

- 介绍 vscode 和 github 社区的…

\#2

上周看 tl/generator 的笔记还留下 iterator 部分的小点，并且对几个 coroutine 初始 flow 有点没搞清楚。

这周给源码加了一些调试语句跑了几遍构造的 test code 配合 reference 的描述终于把之前的疑惑弄明白了， notes 也写好了。

---

好了本周就这样，下周见
