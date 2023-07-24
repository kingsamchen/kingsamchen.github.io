---
title: 一周杂记 in Week 3 July 2023
categories: CODE-LIFE
date: 2023-07-24 21:23:15
tags: [杂记]
---
本周（07/17 ~ 07/23）是7月份的第三周

## Life

\#1

这周看了三部电影：

- Tetris 5/5 作为vb启蒙的代码仔听到那句never underestimate the power of basic怎么也要加一星；什么叫propaganda啊，战术后仰
- 6 underground 3/5 动作场面不错，俩女主可以，贱贱依旧话痨，但是整体确实就那样
- project power 3/5 感觉和 6 underground 差不多，netflix 的模式化电影

后面打算和同事一起去看碟中谍，希望还能来得及排片

\#2

周日下午去同事家贺喜同事乔迁新居，顺带打了一下午德扑。

距离上次打德扑估计都半年多了，虽然开局没多久就 all in 输光了筹码，还好围观了几轮醒了一下脑子重新加入战局后苟了几局后凭借一个大号葫芦直接回本+大赚

最后算下来大概盈利60多，还凑合。

晚上同事请吃辣钟师，味道不错但是确实辣，弄得第二天肠胃还有点不太舒服

\#3

这周杭州下了一周的雨，而且几乎都是在下午的时候开始下暴雨，狂风大作不一会儿就倾盆大雨。

某些地方的道路都被积水给淹了。

看天气预报说下周可能就要因为台风的影响继续来大范围降水?!!

## Work

\#1

本周学习进度

**CppCon 2021 | Lightning Talk: I Wrote a C++ REPL in 20 Lines of Code - And so can you!**

- 一个很有意思的思路实现的 C++ REPL https://github.com/BenBrock/reple

**CppCon 2021 | Back To Basics: Overload Resolution**

- 实话说我不太感冒这种重载匹配规则的研究

**CppCon 2021 | Lightning Talk: Automation and Process to Increase Code Quality - Ira Mykytyn**

- 就 enforcement by automation 的内容，没啥特别的点。不过小姐姐的 talk 还是要鼓励一下

**CppCon 2021 | Extending and Simplifying C++: Thoughts on Pattern Matching using `is` and `as` - Herb Sutter**

- Herb Sutter & Sean Baxster(Circle compiler 作者)搞得 inspect/as/is 实现 pattern matching 支持多种上下文结构的提案展示

**C++ 20 高级编程 | 7. Ranges 标准库**

- 差不多是走马观花介绍了一遍 ranges；并没有很好的阐释

**Modern CMake for C++ | 3. Setting Up Your First CMake Project**

- 不错

DDIA ch7 看了一部分还没看完

\#2

抽点了时间给 esl 的 strings/split 加上了 by-length split，这样刚好 hashids 的 encode_hex 就可以直接用这个功能了

同时去掉了 header guard，全面转向 `#pragma once`

\#3

hashids 水到渠成的直接把剩下的 encode_hex / decode_hex 实现弄完了。

还做了一个简易的 cmd hashids-cli，期间 ubsan 发现了一个bug导致的UB，用 gdb 跟了一下代码很快就找到的原因

---

好了这周就这样，下周见
