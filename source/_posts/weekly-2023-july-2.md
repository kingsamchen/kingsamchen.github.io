---
title: 一周杂记 in Week 2 July 2023
categories: CODE-LIFE
date: 2023-07-17 20:06:50
tags: [杂记]
---
本周（07/10 ~ 07/16）是7月份的第二周

## Life

这周的生活部分有点丰富

\#1

因为之前公司级别的 glint survey 结果不是很好看，中美两边的 manager 一起搞了一个咨文会，做一些解答和反馈的事情。

中间 HZ 这边是把一些出现了很久的不吐不快的事情都讲了，目前看时会有一些进步，但是这个 improvement 能有多少还得拭目以待。

不过不得不说外企的员工关怀做的还是可以的。

\#2

周六去某个同事家撸猫，顺带参观一下联排别墅大house。

同事家里稍微算了一下有7-8只猫，而且基本都是外头捡的，比较符合我的偏好；当真差不多是撸猫撸了个爽。

然后中午一起去附近的商场吃了个火锅，下午坐同事车去了东站的 quantum hub 玩。

路上开了感觉差不多有一个半小时吧，光最后找停车的地方就花了半个多小时... 😒

quantum hub 比较适合亲子活动，很多游戏解密比较低龄；不过有些项目还是挺 high 的。

比较可惜的是因为去排卡丁车比较晚导致我自己没来得及玩上卡丁车就被老婆催回家了 😂

\#3

本周没有家庭影院，因为老婆太忙了，只有周三公司的观影俱乐部看了一部电影：惊天营救2 3.5/5

打斗长镜头可以，但是整体剧本感觉不如1，最后决斗甚至有点降智

## Work

\#1

本周学习进度：

**CppCon 2021 | Back to Basics: Move Semantics - Nicolai Josuttis**

- *The C++ Standard Library* & *C++ Templates* 作者的 move semantics 的基础课
牛逼的是作者还写了一个本专门讲 move semantics 深入的书 *C++ Move Semantics*

**CppCon 2021 | Lightning Talk: So You Thought C++ Was Weird? Meet Enums - Roth Michaels**

- 使用 enum(class) 的一些仍然需要注意的点

**CppCon 2021 | Differentiable Programming in C++ - Vassil Vassilev & William Moses**

- 自动微分计算领域的，完全看不懂…

**CppCon 2021 | Lightning Talk: Zero Cost Regressions - Pejman Ghorbanzade**

- 推荐的 https://github.com/trytouca 这货 Continuous Regression Testing for Engineering Teams

**CppCon 2021 | Code Analysis++ - Anastasia Kazakova**

- 讲了现在有哪些 code analysis tools，这部分很不错

**DDIA | 6. Partitioning**

- 继续干货

**Modern CMake for C++ | 2. The CMake Language**

- CMake 脚本的基本语法，深入程度刚好，而且 variable references 和 scoping 讲的有理有据

\#2

这周写公司某个 golang 的服务的时候遇到一个问题：vscode 里跑的 tests 不会自动去读 shell rc 里定义的环境变量。

研究了一番之后发现其实是需要额外针对环境设置的。

具体可以参考：https://reuvenharrison.medium.com/using-visual-studio-code-to-debug-a-go-program-with-environment-variables-523fea268271

\#3

这周把 hashids 的 encode 和 decode 都写完了，还剩下 encode/decode hex 的部分。

这部分没做的其中一部分原因是因为发现 go-hashids 的实现居然和原版是不一样的，而且也是不兼容的...

有人提了一个 PR 但是这个 PR 看起来已经放了四年多了；所以就...挺有意思的 🤷‍♂️

综合考虑后我打算沿用官方 js 的参考实现，毕竟这样才有通用/兼容性可言。

下周估计就能把所有部分写完了

---

好了这周就这样，下周见
