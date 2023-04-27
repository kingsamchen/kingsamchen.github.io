---
title: 一周杂记 in Week 3 Apr 2023
categories: CODE-LIFE
date: 2023-04-25 23:31:08
tags: [杂记]
---
本周（04/17 ~ 04/23）是四月份第三周

## Life

\#1

因为五一假期调休，这周只有周六一天休息，周日就要开始上班。

真是操了啊。

\#2

这周看了两部电影。

- Boss Level 3.5/5
  剧情挺有意思，我个人也比较喜欢这种风格的影片

- 灌篮高手电影 3.5/5
  周五晚上和同事一起看的，公司电影俱乐部的福利。
  讲道理如果没有情怀，这电影我只能给6分，及格分。因为和之前的 TV 动画割裂太大，细节我也在豆瓣标记的时候吐槽了，这里就不再重复了。

## Work

\#1

本周学习进度如下。

**极客时间 Kafka 技术实战（课程结束）**
    可以作为作者 kafka 书的一个补充，虽然很多内容一样但是新增加了不少更新的点。
    可以和书一起阅读，互相加深

**DDIA**
    开始阅读第四章

**C++ 20 高级编程** 看完了

- 1. 类型与对象

**现代C++白皮书** 看完了

- 1. 前言
- 2. 背景
- 3. C++ 标准委员会

CppCon 20202

- **No Touchy! A Case Study of Software Architecture with Immutable Objects - Borislav Stanimirov**

    一套基于 shared_ptr 实现 shallow-copy-on-read & deep-copy-on-write 的 idiom 来实现（GUI）组件的 immutable 语义。

    不过和 immer 这种提供 immutable containers 不同，talk 里的方案适合给其他对象做一个 immutable wrapper。

    优缺点作者自己都提了，可以作为一种方法/思路的拓宽

- ****C++20 Ranges in Practice - Tristan Brindle****

    讲了一下基本概念，然后通过几个例子来展示 ranges 的强大特性。

    最后一个把具体的 trim 实现为 views 然后直接 pipeline 的例子一下就让人觉得这个特性真叼

\#2

花了点时间把 glog 核心实现看完了。

尤其过了一下 glog 的 signal handler 使用的一些要点。

最终章的 post 也已经更新到了 blog

\#3

这周花了点时间学 OAuth2 的资料，但是因为搜到的一些 Ref 有点杂，加上有其它的事情占据了时间（尤其考虑到本周单休），所以进度一般。

希望下周能有阶段性突破。

---

好了本周就这样，下周见
