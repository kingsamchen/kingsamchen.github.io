---
title: 一周杂记 in Week 5 May 2025
categories: CODE-LIFE
date: 2025-06-03 21:38:36
tags: [杂记]
---
本周（05/26 ~ 06/02）是五月份最后一周，因为周末有端午假日，所以直接算到周一假日最后一天了。

这周突发的事情一个接一个，我快忙晕头转向。

## Life

\#1

老婆在上周日从医院出院之后就立马住进了月子中心。

月嫂阿姨靠谱而且细心，我和岳母轮流陪护，本来理论上应该没啥问题可以比较省心的过完第一个月，结果没想到第一周就出事了。

我妈这个弱智，从浙二看完病回来过了一两天自己身体开始不舒服，我劝说她没事就不用去了，结果为了担心别人说闲话，还是不听我劝，还是一大早就跑到了月子中心。然后被我老婆发现一直在咳嗽，直接委婉的让丈母娘劝她回来。

结果还是晚了一步，傍晚老婆就开始不舒服，晚上买来新冠试剂一测，尼玛阳了...

我妈不信自己是源头，我远程视频指导她用家里的试剂测试，阳性贼明显...

当天晚上联系管理人员紧急把阿姨和孩子放到一个新房间，这个房间我老婆住一晚之后第二天早上回家之后再消杀。

结果更糟糕的发生了：第二天阿姨和我岳母也阳了...只不过阿姨没有症状。

这一圈下来只有我是阴性，宝宝没测但没有发烧也没有症状

然后我岳母又开始骚操作了，坚持说阿姨阳了很危险，宝宝不能阳，一定要换阿姨...老婆冷静下来之后咨询了几个同事，以及同事在邵逸夫ICU的老公，最后联合我把岳母的决策给压下来：不换阿姨，阿姨和宝宝每天身体状况监控，老婆和岳母回她们家静养，我去月子中心辅助。

我赶紧买了一张一等座，把我妈送走，我自己在房子里监控了一天确定都是阴性后立马去了月子中心。

于是 6/1 和 6/2 我每个三小时检查一下宝宝哭是不是要换尿布，然后配合阿姨准备奶。因为半夜也是3小时处理一次，所以两天都没怎么睡好，真的是太累了。

不过好消息是，假期最后一天，老婆已经转阴了，阿姨的阳性那杠也基本看不见了，估计过两天也要转阴了，而且阿姨这几天护理宝宝时候一直戴着口罩；阿姨和宝宝每天的体温都正常

\#2

这周大人鸡飞狗跳，宝宝倒是平稳发育。

第七天脐带那个痂就掉了，而且没有感染。黄疸指标一直都非常正常

唯一的风险就是这周没有母乳喂养，不知道宝宝自身的抗体能撑到多久。

（老婆的说法是至少3-6个月）

周中抽了一天开车去社区医院领了宝宝的疫苗本，整体也比较顺利。

现在就是希望下周各种坑点都尽快过去。

\#3

这周虽然我在休假，但是偶尔也会看一下公司群消息。

周四周五美国那边上班的时候出了产线问题，然后他们一直拖到第二天才解决。root cause是一个非常典型的 redis 部署问题最后导致流量大了之后缓存击穿导致DB异常。

这个我很早很早很早就说了我们的架构这种问题避免不了，然后过了2-3年终于出现了，呵呵

结果是老张被骂了一顿，然后好像又要那我出气了。不知道杭州这边的 local manager 能不能帮我挡掉 🤷‍♂️

\#4

抽了空看了 **灵偶契约 The Boy** 3/5 其实是冲着 The Walking Dead 的 Maggie 去看的。从唯物主义角度来说还是不错的，毕竟很多地方都说通了。但是整体完成度只能说一般，另外 brahms 被近距离捅了两次都没死，这恢复能力堪比金刚狼有点说不过去了吧。

## Work

\#1

**CppCon 2022 | Back to Basics - Name Lookup and Overload Resolution in C++ - Mateusz Pusz** https://www.youtube.com/watch?v=iDX2d7poJnI&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=119

- name lookup 应该是 C++ 中最复杂的特性了，重载加上模板加上各种奇怪的周边规则，例如 ADL，导致这部分有庞大的规则
- 不过里面提到一个实践：如果给一个类加上操作符重载，建议走 hidden friend，即定义在类内部的 friend func，因为 hidden friend 只会被 ADL 找到（看这个[例子](https://gcc.godbolt.org/z/d78381j5r)），减少意外匹配的概率。
- 传统的 ADL 提供的是 customization point，但是使用门槛略高，参考 swap-idiom，核心点是：先 using fallback 的符号，然后用 non-qualified name 去调用
- C++ 20开始随着 ranges，CPO（customization point object）被挖掘出来，核心是利用函数对象来假装自己是个函数，并且因为是函数对象，绕开了 ADL 的一些坑，并且有更灵活的使用场景

**CppCon 2022 | C++ Function Multiversioning in Windows - Joe Bialek and Pranav Kant** https://www.youtube.com/watch?v=LTM1was1dTU&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=121

- 有些高频实用的函数，例如 memcpy，针对不不同架构有各自的实现，怎么做切换。MSVC团队分享了他们的实践
- 首先 if…def 宏是没法 scale 的，trivial 的运行时 selection overhead 太大；MSVC团队的做法没看太懂，大致是编译器开个洞，提供 [[msvc:map(isa → fn)]] 这种形式的 attribution，然后运行时第一次做一个 selection，最后利用 thunk 替换上去
- 操作上感觉有点像 Windows DLL 的 delay load 的做法，但是没具体研究过这方面，摊手

**C++ Weekly - Ep 341 - std format vs lib {fmt}** https://www.youtube.com/watch?v=zc6B-j0S9Iw

- std 的 format 的一些功能进入标准会晚很多，例如 `print()` 要一直到 C++ 23，并且进入标准库之后需要保证 ABI 兼容性…
- fmtlib 更新得更快，而且目前连 constexpr 都做好了

**C++ Weekly - Ep 340 - Finally! A Simple String Split in C++!** https://www.youtube.com/watch?v=V14xGZAyVKI

- 介绍的 ranges/view 引入的 std::ranges::views::split
- 不过这套东西其实入手门槛还是挺高的

**C++ Weekly - Ep 339 - `static constexpr` vs `inline constexpr`** https://www.youtube.com/watch?v=QVHwOOrSh3w

- 如果定义在 header file 中用 inline constexpr，否则每个TU都有一份会造成资源浪费
- function scope 的话用 static constexpr
- 研究了一下函数级别的 constexpr/static constexpr 发现后者确实对于 non-trivial 类型有一些优化效果，比如 std::string_view

\#2

从 C++ Weekly - Ep 341 - std format vs lib {fmt} 里学到的，fmtlib 现在的 constexpr 可以这么玩

```cpp
constexpr auto get_string(int value) {
  std::array<char, 100> result{};
  fmt::format_to(result.begin(), FMT_COMPILE("Hello {}"), value);
  return result;
}
```

\#3

从 **CppCon 2022 | Back to Basics - Name Lookup and Overload Resolution in C++ - Mateusz Pusz** 里学习了一下 CPO 的使用，并且研究了一下如何用 SFINAE 来模拟 is_tag_invocable。

相关代码在 https://github.com/kingsamchen/Eureka/pull/38

C++20 的 concepts 真是好用啊。。。

---

好了这周就这样，下周见
