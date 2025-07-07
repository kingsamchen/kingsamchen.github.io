---
title: 一周杂记 in Week 1 July 2025
categories: CODE-LIFE
date: 2025-07-07 22:27:04
tags: [杂记]
---
本周（06/30 ~ 07/06）是七月份的第一周，炎炎夏日依然到来。

## Life

\#1

周中因为比较忙所以没去媳妇儿家看完，于是到了周末立马两个白天都在媳妇儿家里给屎总打下手。

一周没见，感觉屎总又长大了一些，而且脾气渐长中又透露出一丢丢的鸡贼。

感觉叫石榴的，不仅脾气极差，嗓门还贼大 🤣

当了两天实习生感觉学会了不少，已经可以给屎总喂奶了，而且竖抱也能把娃哄睡着了；还和媳妇儿第一次配合，在屎总“不辞辛苦”地拉了托大的之后，给清理的干净清爽。

就是感觉屎总这太像我了，平时睡得不沉，或者说警惕性太强，稍微一点风吹草动就能醒过来。而且醒来的时候发现周围没人就不爽开始发脾气大哭，在满足她的要求之后立马变乖巧脸。

以前石榴和小花感觉都没她这么鸡贼 🤔

后面打算周中 WFH 的时候去一天，然后周末有时候两天，有时候一天，至少得把陪伴贯彻下去

\#2

这周乐刻在城西银泰的新门店终于开了，于是去跑了一次步。

设备都是全新的，这点很好，而且地方也还挺大，多了很多新设备。

体验下来感觉目前有两个大坑点和一个小坑点比较难受：

1. 空调太少了，导致人多的时候特别热，尤其有氧区，跑下来感觉都要中暑了。
  店长说新加的空调在路上了，过两天就就位，希望这个问题后面能解决
2. 厕所太远了...
  因为新店在银泰里，所以自然选择的复用商场的厕所；但是把4楼的厕所离健身房的位置也太远了，来回感觉至少要7-10分钟在路上。。。
3. 电梯太慢，因为是商场合用的，然后没有自动扶梯；所以如果不想等就自己爬楼梯。

这次周日时隔接近一个月忧伤了一次BP，而且不仅强度直接到位，还因为是新课程，感觉强度比老版要大不少。之前最后一节核心课还可以放松划水，现在直接变成最后的杠铃提拉耐力课了。。。

周日上午练完下午肌肉就开始酸痛了 🫠

\#3

这周参加公司电影俱乐部，看了

- **F1：狂飙飞车** 5/5 可能是今年最好的商业片(之一?) 别管剧本是不是老套，就说这套路剧本的视听演绎各方面是不是足够扎实。继当年三井寿那句“教练我想打篮球”之后现在又有六旬老帅皮的“老板我想开F1”，全片荷尔蒙爆炸。FYI: Kate我总觉得哪里见过，看了演员表发现原来是better call saul 里老麦克的儿媳啊

## Work

\#1

**CppCon 2022 | Managing External API’s in Enterprise Systems - Pete Muldoon** https://www.youtube.com/watch?v=cDfX1AqNYcE&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=50

- 讲了很多服务API变更上的东西，但是整体比较细碎，没有一个体系
- 一些 tips 还是有帮助的，比如上新服务实现（重构/重写？）好歹按比例灰度吧，别一上来全切流量

**CppCon 2022 | MDSPAN - A Deep Dive Spanning C++, Kokkos & SYCL - Nevin Liber** https://www.youtube.com/watch?v=lvylnaqB96U&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=48

- 大部分内容还是 mdspan 这个提案的发展历史，可以当作故事会看看

**CppCon 2022 | Cross-Building Strategies in the Age of C++ Package Managers - Luis Caro Campos** https://www.youtube.com/watch?v=6fzf9WNWB30&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=104

- conan（部分涉及vcpkg）支持 cross building 的一些实践

**C++ Weekly - Ep 486 - Captureless Lambdas with Captures** https://www.youtube.com/watch?v=MvjBJmsbM4g&t=1s

- 整活系列，图一乐就行
- constexpr 优化的 local var 会被 elevated，lambda 内访问它不会被认为是 capture，然后就可以整活了

**C++ Weekly - Ep 322 - Top 4 Places To Never Use `const`** https://www.youtube.com/watch?v=dGCxMmGvocE

- 核心总结就是只要这个变量可能会被 move-applied 就不要 const 修饰

**C++ Weekly - Ep 321 - April 2022 Best Practices Game Jam Results** https://www.youtube.com/watch?v=TQTb6ewowtk

- 这一期比较水，分享 Jason 做这些游戏中的一些想法

**C++ Weekly - Ep 320 - Using `inline namespace` To Save Your ABI** https://www.youtube.com/watch?v=rUESOjhvLw0

- 核心是使用 inline namespace 对于符号的代码中的访问没有区别，但是产生的符号名会带上 inline namespace 的名字，所以如果出现 abi mismatch 可以直接触发链接错误而不是 silient ub

**C++ Weekly - Ep 319 - A JSON To C++ Converter** https://www.youtube.com/watch?v=HROQPE59q_w

- 这个不是 json serde 哦，而是给定现成的 JSON 数据文件，直接生成对应的 cpp 的代码，数据都是确定写死在代码里的，而且都做了 constexpr

**Deferred argument evaluation** https://bannalia.blogspot.com/2022/10/deferred-argument-evaluation.html

- 前半部分是前情提要，就是因为想用 try_emplace，但是那个 element 只有在有需要的时候才应该创建，否则是个浪费，于是引出了下面这个

    ```cpp
    template<typename F>
    struct deferred_call
    {
      using result_type=decltype(std::declval<const F>()());
      operator result_type() const { return f(); }

      // "silent" conversion operator marked with ~explicit
      // (not actual C++)
      template<typename T>
      requires (std::is_constructible_v<T, result_type>)
      ~explicit constexpr operator T() const { return {f()}; }

      F f;
    };
    ```

- 结构内存了一个 callable，利用类型转换的时机调用创建对象；不过这里有个 trick 是利用了 try_emplace 本身是个 function template，否则调用时机直接就是在传参期间了

**On the overloading of the address-of operator & in smart pointer classes** https://devblogs.microsoft.com/oldnewthing/20221010-00/?p=107269

- Windows 上某些 library 会给提供的智能指针重载 `operator&` 然后各个库的行为不一样，大多数对应的是标准库的 `release()` 语义
- 不过实话说这个重载是真的有点迷糊，可以作为一个反面例子

\#2

这周比较密集的花了点时间把以前 cmake 上想做的点都做了

- common compile configs 现在可以直接支持 interface，如果 target 是 interface 的话只设置几个基本的 definitions；以 esl 为例子的话见 https://github.com/kingsamchen/esl/pull/24
- 研究了一把 cmake 的 pch，不再是以前野路子的方案，见 https://github.com/kingsamchen/Eureka/pull/44；另外有个其他项目的参考 https://github.com/ThorsteinnJonsson/cmake_precompiled_header_example
- anvil 做了大调整，不仅把上述两个都做了支持，还做了对单独增加 module 的支持；同时因为有这个支持，以前很奇怪的 test support 那部分都去掉了；见 https://github.com/kingsamchen/anvil/pull/22

后面如果公司项目 remake 需要类似的工具的话，估计会直接拿 anvil 为蓝本搞一个。

不过因为已经固定是公司项目使用了，所以灵活性就不用考虑那么多，反而会结合我们实际使用情况让工具变得更加容易使用

\#3

这周公司的项目已经开始灾后重建了。

第一步就是拆服务，拆服务的前置条件就是各种依赖理理清楚，然后一边踩坑一边尝试把一个看起来最实际的模块给拆出来

目前看起来应该是个好开始 🤔

---

好了这周就这样，下周见
