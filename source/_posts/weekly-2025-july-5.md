---
title: 一周杂记 in Week 5 July 2025
categories: CODE-LIFE
date: 2025-08-04 23:26:22
tags: [杂记]
---
本周（07/28 ~ 08/03）是7月份最后一周，这周五已经是8月1号了。

这周公司的事情直接忙傻了

## Life

\#1

这周实在是太忙了，而且基本都是在做写汇报材料的事情，来回折腾

之前说到新 Head 和杭州这边新 mgr 路线不太一致的问题，这个问题导致了很多衍生的问题。

作为一线干活的被夹在中间是真的难受

\#2

这周和媳妇儿和解了，周末两天又跑到媳妇儿家带娃。

电视的事情就先过去了，而且媳妇儿说她做了研究，娃两岁前最好不要接触电视，所以电视2年后再说。

这周去带娃又成功顺利的给娃喂了几次奶，感觉屎总长得越来越快了，越来越清秀，就是偶尔做表情的时候挺抽象。

这周因为丈母娘之前单位的领导来杭州看娃，顺带逛一下杭州，所以这两天我去那边帮忙差不多是刚需，不然所有事情媳妇儿一个人搞估计真的扛不住。

\#3

这周五去医院做了体检，因为8点不到就到了医院，所以整体流程还算比较快，花了接近一个小时都做完了。

不知道这次结果如何 🤔

## Work

\#1

**CppCon 2022 | The Surprising Complexity of Formatting Ranges in Cpp - Barry Revzin**

https://www.youtube.com/watch?v=EQELdyecZlU&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=99

- 前半部分自定义 fmt 的 formatter 还是容易懂的，而且适用面广
- 后面要微操自定义 fmt spec 给自定义的 range types 就比较复杂了；等真的需要的时候可以回过头来再看

**A “pick two” triangle for std::vector** https://quuxplusone.github.io/blog/2022/09/30/vector-pessimization-pick-two/

- 核心就是 vector 的 strong exception guarantee / moving during memory reallocation / exception propagation 三者构成不可能三角，只能选其二
- 而且 std::vector 直接先选了 strong exception guarantee

对，这周总共就俩...因为前面说了，是真的太忙了。

\#2

这周抽了点时间把之前写的 http router tree/node 给加到 fawkes 了，并且做了一些调整，还研究了一下 CMake 中 OBJECT_LIBRARY 的用法

因为这周真的太忙了，所以 router 都还没来得及封装，也只能放到后面了。

\#3

这周在公司抽了点时间利用 Python 做了一个把 Helm chart values 转换成我们公司内部云平台用的一套配置值格式的东西，整体还算比较顺利。

一些东西用 Python 写配合各种奇怪的三方库，在不考虑性能的情况下真的是嘎嘎快

---

好了这周就这样，下周见
