---
title: 一周杂记 in Week 2 Jan 2025
categories: CODE-LIFE
date: 2025-01-12 23:30:07
tags: [杂记]
---
本周（01/06 ~ 01/12）是新年1月份第二周，离春节回家又近了 🤔

## Life

\#1

这周乐乐恢复的不错，伤口的腐烂已经停了，烂肉基本都掉光了，买了清创消毒和去腐生肌的粉之后有明显改善，已经开始长肉了，就等后面慢慢恢复了。

而且这家伙是真能吃，一周涨了0.6kg，脾气也不小，放出来溜达之后你给她塞回笼子里都能跟你急

不过也确实花钱，这第一阶段结束了，花了2006，还是算上了流浪猫折扣

后面就希望第二阶段能快快恢复，一周左右就能好到差不多可以绝育手术的状态

\#2

公司周二年会，但是不同以前，这次下午搞了一个趣味运动会，每个部门派人组队参加。

我也报名了，参加了一个项目，本来想多搞几个项目的但是看对面竞争力太强担心拖后腿就当观众了。

我们 team 最后拿了第五名，而且还是同分但是小分劣势。奖金5000，自己组可以分到2K，算是不错了，刚好Q4团建可以加餐 😊

然后晚宴还是去年那家酒店，菜嘛一样烂，都是预制的，真的不好吃，羊排还是冷的，整个大无语。

\#3

之前让媳妇儿申请一下她们医院的停车场牌照，这样节假日可以把车停医院然后步行到地铁站到东站。

周四说申请弄好后周五打算去踩个点熟悉一下流程，然后第一次我自己跟着导航直接在重点附近迷路了，导航说已经到达目的地附近就退出了。

我莫名其妙进了一个小区然后发现不对折腾了半天又开了出来，接上媳妇儿后又重新绕了一遍。发现停车场居然尼玛是那个小区边上的一个入口，而且那个道窄的是个单行道...

因为周五已经很堵了加上回去路上连烤鸭都没吃上，给我气炸了 🤬

\#4

周六当了一把司机，带着媳妇儿和她科室里同事一家人开车到富阳一个别墅参加媳妇儿他们科室的团建。

烤全羊是真不错，然后其他人包的饺子也好吃，烧烤也在水准之上。

不过午饭过后和媳妇儿因为太困了找了一个房间睡着了错过了其他人的舞蹈表演 🤣

还好后面的套圈节目套中了一盒欧舒丹小样

不过媳妇儿说这玩意儿她用的不是很习惯

\#5

这周开始玩 Darksiders: Genesis，操作方式和前作完全变了，视角变成了以前日式RPG那种，加上动作游戏的风格操作真的有点难受 🤷‍♂️

不过玩游戏真开心啊，哈哈哈 XD

## Work

\#1

**CppCon 2022 | Understanding C++ Coroutines by Example: Generators (Part 1 of 2) - Pavel Novikov** https://www.youtube.com/watch?v=lm10Cj-HNKQ&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=77

- 以 generator 为例子讲解 c++ coroutine 基础

**CppNow 2024 | A Deep Dive Into C++ Object Lifetimes - Jonathan**  Müllerhttps://www.youtube.com/watch?v=oZyhq4D-QL4&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=54

- 干货很多，门槛也不低，不过讲道理不搞 raw memory 的话也基本不怎么会遇到这种问题
- 好处是起码知道了 launder 是用来处理 provenance 的

**C++ Weekly - Ep 461 - C++26's std::views::concat** https://www.youtube.com/watch?v=QeWdhHyBBv0

- `views::concat` 可以联结多个容器，但是访问方式会被限制为容器中最严格的那个
- 比如如果 concat(vector, array, list) 则联结的结果不能使用 random access 因为 list 不支持；如果只是顺序迭代的话则没有问题

**C++ Weekly - Ep 412 - Possible Uses of C++23's [[assume]] Attribute** https://www.youtube.com/watch?v=Frl8XKhvA4Q

- 看完之后发现我之前有点误解了 `[[assume]]` 这个不是 contract 那种东西，而是给编译器一个非常强的 assumption 让他按照这个假设去优化生成代码
- 所以如果你的 assume 是不对的不准的，那么生成的代码就是有问题的，甚至都不是常规那种UB的情况
- 评论区有提到一个使用场景是 `assert(x)` 宏在 `NDEBUG` 定义的情况以前是 no-op 现在就可以替换成 `[[assume(x)]]` 来让编译器按照这个假设生成更优化代码；因为通常来说 assert 都违反了已经是很严重的问题了程序应该立即崩溃退出，所以从这个点出发让编译器按照这个优化也没什么问题

**C++ Weekly - Ep 411 - Intro to C++ Exceptions** https://www.youtube.com/watch?v=uE0h79vB-rw

- TIL reuse handler 也可以不用包一层 exception_ptr，只要在 nested context 然后直接 `throw` rethrow 就行…

**C++ Weekly - Ep 410 - What Are Padding and Alignment? (And Why You Might Care)** https://www.youtube.com/watch?v=E0QhZ6tNoRg

- 介绍 memory padding & alignment

**C++ Weekly - Ep 409 - How To 2x or 3x Your Developer Salary in 2024 (Plus Some Interviewing Tips!)** https://www.youtube.com/watch?v=jWhFuK7J5HY

- 核心还是打造个人品牌，持续不断参与社区
- 不过感觉不一定适合外企大厂？

**An Attempt to avoid C++ exceptions** https://genodians.org/nfeske/2021-11-26-attempt-no-exceptions

- genode 这个项目是 OS framework 和普通业务不太一样，所以他们发现抛异常引起的内存分配可能会二次触发异常
- 所以他们的解决方案是自己做了一个有点奇怪但是可能比较适合他们场景的东西
- 讲道理我觉得他们这个东西通用性体验可能还不如 `std::expected<T, E>`
- 另外这个他们的业务具有特殊性所以可能异常确实不合适，但是他们这个方案是真的丑而且罗嗦，拿来写业务估计也要吐血了，成功的handler放到了一个 lambda scope 这样多层调用直接变成 callback-hell 那样了，好歹糊一层 monadic 啊

**Uninitialized Stack Variables** https://www.netmeister.org/blog/stack-vars.html

- 忘了初始化变量还煞有介事的查了半天内存…
- 总结就是：告警打开 + tidy 打开 + sanitizers 打开

**What are premature optimizations?**  https://johnysswlab.com/what-are-premature-optimizations/

- 这篇的观点有点意思：虽然优化 hot code 的效果最明显，但是不代表可以随便乱写代码
- hot spots 很多时候不是能预测的，并且可能随着不同的操作会引发不同的 hot spots，对大型软件来说这个是常见现象
- 有一些好的日常编码习惯可以避免写的代码在日后变成 hot spots 的一部分

**A note on namespace __cpo** https://quuxplusone.github.io/blog/2021/12/07/namespace-cpo/

- 没有解释什么是 CPO 解释是为什么要对 CPO 引入一个 inline namespace
- 原因是有一些 identifier 可能会被 friend function，不引入 inline namespace 的话会导致名字查找会把 CPO 变量名和 friend function 认为是冲突的

**Modernizing your code with C++20** https://www.sonarsource.com/blog/modernizing-your-code-with-cpp20/

- 一些引入 C++ 20 之后的 best(better) practices 并且他们家的工具能做到的 auto-fix
- 算是一次成功的广告？

\#2

最近玩游戏比较 high，先继续松懈先 🤡

---

好了这周就这样，下周见
