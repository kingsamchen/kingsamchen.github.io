---
title: 一周杂记 in Week 2 Oct 2024
categories: CODE-LIFE
date: 2024-10-14 20:33:02
tags: [杂记]
---
本周（10/08 ~ 10/13）是十月份的第二周。假期结束了，周末也要调休上班了 😅

## Life

\#1

这周虽然是周二开始上班，但是因为周六要调休上班所以不仅实际上还是上了5天班，而且因为周末变成了单休，所以变得非常的疲倦和累。

尤其人到中年，身体的耐受性真的不如二十多岁年轻的时候啊。

周五在家办公，中午去了健身房练了一下有氧，比较保守的10kmh配速跑了5km，然后想着还得加强一下力量，就去做了腿推。

没想到最后一组 130KG x 20 做完时候瞬间感到有一点点不舒服，踉跄站起来的时候差点腿软地站不住。慢慢走到墙边靠着想休息一下，但是不适的感觉越来越明显。

瞬时感觉有点头晕，喘气上不了劲，有点想吐。感觉可能有点低血糖或者刚才一不小心岔气了，更蛋疼的时候这个时候肚子还有点想腹泻...

勉强换好衣服后立马在饮料机买了一瓶东鹏特饮，觉着得赶紧先补充一下血糖。

喝完又休息了一两分钟感觉情况没有恶化之后就回家了；到家之后立马进卫生间腹泻，马桶上做了大概十来分钟，慢慢感觉有点好转了。

完事后赶紧洗了一个热水澡，洗完之后觉得应该是恢复了。于是立马点了一份 KFC 和牛汉堡套餐，免得过会儿又开始缺能量

讲道理这次练的是真的有点后怕，下回上大负重不能一组做这么多个，容易出事 🙄

\#2

因为媳妇儿怀孕后尿频症状很明显，所以索性周四上午请了半天假陪她去市妇保做一个检查，顺带把第一次孕检做了。

一开始想去钱江院区，但是好巧不巧碰到早高峰，所以临时改了主意去城西院区。离家近，开车十分钟就到了；虽然这家只能做常规检查，但是目前对我们来说已经足够了。

做完各项检查后结果一切正常，没有感染，并且现在六周已经有胎心了。两人高高兴兴就回家了。

结果到了周日早上，媳妇儿说早上有点腹部绞痛，还有出血，给我吓得立马起床穿衣洗漱，然后带着她开车去市妇保钱江院区；路上因为紧张还开错路，掉头绕了一段。

至于乐刻团课没法退，已经完全不 care 了。

还好查完医生说不是很严重，开了点孕酮让在家里休息几天，注意有没有止血就行。

孕早期实在是有点脆皮了，一点风吹草动都有明显反应。

\#3

打算响应政府的消费号召，这次双十一配台新的主力台式机，预算大概4W以内。

这几天问了几个刚配过台式不久的同事配置单，又找了大学同学O哥出谋划策，先把基本配置单拟好。

后续等双十一临近，消费券下发之后再出手 😋

\#4

本周太忙了，没电影看。

不过我把铁血战士的电影都下完了，等异形系列补完就看这个。

## Work

\#1

**C++ Templates 2nd | 16. Specialization and Overloading**

- 多个同名函数模板构成 overload resolution set 时，即使有多个 candidates 签名一致，more specialized one 还是会认为是更好的结果
- class full specialization / function template full specialization / full member specialization
- partial class specialization

**CppCon 2022 | The Imperatives Must Go! [Functional Programming in Modern C++] - Victor Ciura** https://www.youtube.com/watch?v=M5HuOZ4sgJE&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=46

- 讲FP的，但是这个talk感觉入门嘛入门没讲好，应用嘛应用没讲好…

**CppCon 2022 | Nobody Can Program Correctly - Lessons From 20 Years of Debugging C++ Code - Sebastian Theophil** https://www.youtube.com/watch?v=2uk2Z6lSams&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=48

- 讲 trouble shooting 的一些方法论，问题就是太方法论了，显得不太实用，有点干
- try to reproduce, and use tools, like sanitizers, if any may help
- use debugger, logging tools to help identify the bug
- even git bisect could help

**CppNow 2024 | C++ Coroutines and Structured Concurrency in Practice - Dmitry Prokoptsev** https://www.youtube.com/watch?v=sWeOIS14Myg&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=16

- 作者来自 hudson trading，讲的是他们写了一个library/framework，基于 ASIO 和 C++20 coroutine 做了 structural concurrency 的实践
- 比如用 nersury 来管理 coroutine，如何实现 cancellation .etc
- bar 还是很高的，估计还是得抽时间看代码才能get到精华

**CppNow 2024 | Value Oriented Programming Part V - Return of the Values - Tony Van Eerd** https://www.youtube.com/watch?v=sc1guyo5Rso&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=14

- 讲的比较零散，核心基本就是使用值语义比起面向对象那套引用语义的好处

**CppNow 2024 | Expressive Compile-Time Parsers in C++ - Alon Wolf** https://www.youtube.com/watch?v=TDOu1RKaNR8&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=15

- 介绍了多个 compile-time parsers 跨度从03的 boost.spirit 到最新的 23 的 experimental projects
- compile-time parser 可以搭配实现 compile-time code-generation / reflection

**C++ Weekly - Ep 449 - More constexpr Math!** https://www.youtube.com/watch?v=reWnel5uLS4

- 除了 C++ 20/23 新增加的 constexpr math 函数，还有个 GCEM 库，这个库只要 C++ 11 就能用

**C++ Weekly - Ep 445 - C++11's thread_local** https://www.youtube.com/watch?v=q9_vljSaBDg

- 介绍 thread_local
- 函数中 `static thread_local` 和 `thread_local` 本质上没区别

**C++ coroutines: Accepting types via return_void and return_value** https://devblogs.microsoft.com/oldnewthing/20210407-00/?p=105061

- 实现 simple promise 的 return_value
- 顺带带你复习 reference collapsing rule（不过里面 T 不是类模板参数吗，这也能触发 forward reference？）

**C++ coroutines: Awaiting the simple_task** https://devblogs.microsoft.com/oldnewthing/20210408-00/?p=105063

- 实现 simple_task 的 awaiter

**C++ coroutines: Managing the reference count of the coroutine state** https://devblogs.microsoft.com/oldnewthing/20210409-00/?p=105065

- decrement 的 memory fence 用的有点tricky

**C++20 Concepts — Complete Guide** https://itnext.io/c-20-concepts-complete-guide-42c9e009c6bf

- 出乎意料的高质量，从如何使用 concepts 出发，引申出如何定义自己的 concepts 然后再按照一些比较常用但是稍微复杂的点展开

**Empty Base Class Optimisation, no_unique_address and unique_ptr** https://www.cppstories.com/2021/no-unique-address/

- 介绍利用 EBO 优化的 compress_pair
- C++ 20 之后可以直接用 `[[no_unique_address]]` 了，直接省事

**浅谈The C++ Executors** https://zhuanlan.zhihu.com/p/395250667

- 讲了一下这块提案的历史以及 S&R 区别于 future/promise 模型的地方

\#2

这周本来打算抽点时间看一下 https://github.com/jeremyong/coop 这个repo 的代码，学习一下如何结合 coroutine 和系统事件循环的，但是因为只有一天休息并且周日事情太多，所以就先跳过了。

下周抽时间再搞吧~

---

好了这周就这样，下周见
