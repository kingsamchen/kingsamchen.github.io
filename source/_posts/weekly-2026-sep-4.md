---
title: 一周杂记 in Week 4 Sep 2026
categories: CODE-LIFE
date: 2026-09-29 23:08:03
tags: [杂记]
---
本周（9.21 ~ 9.27）是9月份第四周，也是最后一周，下周周四就进入 10 月份

## Life

\#1

上周调休周日上班，这周上到周四就开始进入中秋假期。

所以其实本质上来说假期并没有多，反而因为调休搞得非常疲倦

中国人是真的天生牛马体吗，要这样折磨..?

\#2

这周假期第一天周五下午带着秋宝去了良渚的永旺梦乐城的 Nitori 转转

其实主要是丈母娘对媳妇儿新给她买的床和床垫，尤其是床垫非常满意；打算去店里再看看，给灵溪家里也搞两张

秋宝在店里非常兴奋，几乎是跑着走的来来去去，媳妇儿和丈母娘要一直盯着和跟着；媳妇儿说真有点跟不上秋宝的节奏了 🤡

不过这天比较热，所以我们逛了大概两小时就开车回家了，到家之后大家都非常累，不过好事是秋宝入睡也容易了 😂

\#3

这周日和媳妇儿去了浙大图书馆学习，只能感慨一下浙大的图书馆确实牛逼，数字化做的太强了

位置都可以在手机上远程预约，1F还有校内麦斯威的咖啡饮品品牌，不过他们用的豆子味道一般就是 😂

跟着媳妇儿在图书馆从上午9点多呆到下午4点，番茄时钟都刷爆了，用公司的电脑“加班”做了好几个 tasks 🤣

结束后开车去了西站踩了个点，因为后面 10.2 要开车带着娃去西站坐高铁

\#4

这周拳击课继续上了一节，新增加的内容是侧踹。

不过我因为大脑前庭问题，导致单腿站立不能特别稳，所以这个还需要非常多的练习才行。

观影的话，还在继续看B站UP的POI解说~

## Work

\#1

**Boost Libraries | Basic HTTP and WebSocket Programming with Boost.Beast** https://www.youtube.com/watch?v=gVmwrnhkybk&list=PLJDO7P5jAoXznYam4ucdMMcKJky8UHbhC&index=5&t=1426s

- beast 的 http server / websocket server 介绍；sample 应该是官方的例子里改的
- 感觉不错，适合新手看

**CppNow 2025 | C++ Generic Programming Considered Harmful? - Jeff Garland** https://www.youtube.com/watch?v=jXQ6WtYmfZw&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=28

- 这个算是标题党吧
- 讲了非常多的东西，核心是 all tools have benefits & limitations；generic is also a tool
- 很多以前观念里慢的东西其实并不慢，包括但不限于，虚函数，异常 .etc
- 这个 talk 更多还是 inspiration

**C++ Weekly - Ep 551 - static const is a Code Smell?** https://www.youtube.com/watch?v=cXjY5fWUv7g

- `static constexpr` 是正常且 preferred adoption
- `constinit` 只要求用来初始化的值是 compile-time，变量自身不是 `const` 可以被修改
- `const constinit` 其实就是 `constexpr`
- 最后再考虑 `static const`

**C++ Weekly - Ep 77 - G++ 7.1 for DOS** https://www.youtube.com/watch?v=yHrC_rZUaiA

- djgpp for dos 🤡

**C++ Weekly - Ep 76 - static_print** https://www.youtube.com/watch?v=61w4LbQ0fzU

- 一个 custom patch 可以在编译期打印一些东西
- 当然这个提案最后没有进入标准

**C++ Weekly - Ep 75 - Why You Cannot Move From Const** https://www.youtube.com/watch?v=ZKaoR3dP9uM

- 对 const object 施加 std::move 会出来 const T&&，然后匹配到 copy ctor
- 这个应该是常识了吧

\#2

这周依旧紧张地在给 scheduler 做 apache/httpd 到 fawkes new http server 的迁移。

因为10月份有一整周的国庆假期，所以国庆假期回来之后第二周就要立马 Oct Release 了，所以总感觉这次又要赶不上了...

尤其目前还没开始到迁移具体的 handler 呢...

\#3

抽了时间给 fawkes 同步了一些 patches/changes

https://github.com/kingsamchen/fawkes/pull/34 和 https://github.com/kingsamchen/fawkes/pull/35

主要是第二个

---

这周就这样，下周见~
