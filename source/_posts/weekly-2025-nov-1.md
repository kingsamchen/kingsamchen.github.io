---
title: 一周杂记 in Week 1 Nov 2025
categories: PROGRAMMING
date: 2025-11-10 22:34:35
tags: [杂记]
---
本周（11/03 ~ 11/09）是11月份第一周，这周雨水突然多了起来。

## Life

\#1

这次购物季叠加了双十一，国补，还有需要给娃买各种东西，所以开销不少。

还给自己买了一把 Herman Miller Cosm 想体验一下和 Aeron 有什么不同 🤣 Aeron 后面就运到办公室当个人日常办公用椅了，每天在公司坐的时间那么久，是得用把舒服的椅子

\#2

周三周四晚上花了好大经历拼好了老婆给娃买的大边床，还有围栏

但是周五的时候媳妇儿说她觉得那个床不太行，还是想退货了去线下看看 babycare 的大号婴儿床...

于是周六开车去了西溪印象城，线下挑选；挑选过程比较顺利，不过老婆觉得线上下单优惠更多，于是晚上回去才下单，但是实际上也就便宜了100...

一个插曲是在山姆买了几袋大米，但是出来等电梯发现人太多了，受不了，于是折腾了一大圈扛着大米走了扶梯。。。

之后老婆表示以后再也不去线下山姆了，人太多了，结算太麻烦了，搬运东西太蛋疼了

以至于我们气到晚饭都没在商场吃就回来了

\#3

周六第一次上了拳击训练课，感觉有点意思，第一次真的带着拳套和护具练习打拳。

不过体能消耗是真的大，1hr50mins下来感觉消耗不比有氧跑少。

不过因为教练下周有空，再约估计得要下下周了。

\#4

本周电影俱乐部

- **巴黎夏日 Le Rendez-vous de l'été** 4/5 喜剧的内核是悲剧 拨开亲情的外壳里出来的事冷漠和毫不关心 对一个外地法国人来说巴黎远没有自己声称的包容，在巴黎感受到最真诚的关怀不是来自同父异母的姐姐反而是毫无关联的陌生人


## Work

\#1

**CppCon 2023 | Exceptionally Bad: The Misuse of Exceptions in C++ & How to Do Better - Peter Muldoon** https://www.youtube.com/watch?v=Oy-VTqz1_58&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=10

- 这哥们的演讲能力真的牛逼
- 这个 talk 给了不少使用 exceptions 的建议，不建议使用 exception 的 scenarios 如下：
    - loop 中不要拿 exception 做 flow control，talk 里用的例子是 retry
    - 如果 call stack 比较浅，那么考虑可以用其他替代；例如 std::exepcted / std::optional 甚至 bool
    - 总结就是预期发生频率比较高的不要用 exception，把 exception 留给 unexpected errors 和 serious errors
- 使用 exceptions 的时候 mutable logging 是一个作者肯定的实践
- 另外提供了一个 OmegaException base class，额外夹带了 source_location / stacktrace 甚至 extra data；不过考虑到 exception safety，额外数据要避免在 create/copy 时可能发生异常

**CppCon 2023 | A Journey Into Non-Virtual Polymorphism in C++ - Rudyard Merriam** https://www.youtube.com/watch?v=xpomlTd41hg&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=11

- 非常老派的一个开发者的分享，如何不用 virtual functions 来实现 (ad-hoc) polymorphism
- 实际上重载函数也是一种 polymorphism；any, variant, tuple 都算是
- std::apply 搭配 overload idiom 的做法有点意思，后续研究一下
- 另外 CRTP 类型不借助 template/concepts 是做不到 compile-time polymorphism 的，只能有实现复用的效果；演讲里提到的一个方案是搭配 std::variant，而且 variant 里塞得是指针 🤣

**C++ Weekly - Ep 504 - Practical Reflection in C++26** https://www.youtube.com/watch?v=Mg_TBYppQwU

- Jason 利用 C++ 26 的 reflection 做了一个 DSL evaluator
- 这个可以展示 static reflection 的上限可以多高

**C++ Weekly - Ep 241 - Using explicit to Find Expensive Accidental Copies** https://www.youtube.com/watch?v=5wJ-jKK_Zy0

- 把 copy ctor 标记成 explicit，这样在语法层面需要手动调用 copy constructor
- 不过我觉得更好的做法可能是吧 copy ctor 变成 private 然后提供一个 clone 这样的函数，实践上可能更好一些

**C++ Weekly - Ep 240 - Start Using [[nodiscard]]!** https://www.youtube.com/watch?v=nhsahjY5jdE

- 用 `[[nodiscard]]` 可以减少那些命名上不太友好的函数被误用的可能

**C++ Weekly - Ep 239 - std::mem_fun vs std::mem_fn Fight!** https://www.youtube.com/watch?v=geE-LyR_NrM

- std::mem_fun 是 C++ 11 之前引入的，只能用于绑定0个参数和只有1个参数的成员函数的情况
- 所以 C++ 11 引入了一堆新东西之后这个就可以退出历史舞台了

**C++ Weekly - Ep 238 - const mutable Lambdas?** https://www.youtube.com/watch?v=qXy0facG6cA

- const lambda 指的是 lambda 本身是 const 的，所以如果又给 lambda 标记成了 mutable，编译直接失败

**记一次CLOSE_WAIT引发的血案** https://kz.cx/archives/9753.html

- 原文估计涉及敏感信息被作者删了，这个是转载的
- 核心就是某个服务在断开连接阶段没有处理好 fd 的释放问题，导致一直卡在 CLOSE_WAIT 上
- 另外这篇文章用的排查命令主要是 `ss` 这个命令被认为是 `netstat` 的替代品，性能更高并且支持高级的过滤功能

\#2

给 fawkes 做了 route handler 以及 user handler coroutine 化的改造

PR 见 https://github.com/kingsamchen/fawkes/pull/8

这个 PR 里顺带去掉了两条 coroutine 相关的 tidy rules:

- cppcoreguidelines-avoid-capturing-lambda-coroutines
- cppcoreguidelines-avoid-reference-coroutine-parameters

去掉的原因是这两条 rules 不太适合 fawkes 的使用场景，而且 asio::awaitable 的设计上不太容易踩到这里的坑。

不过为了充分理解可以去掉这两个 rules，顺带复习了一下 C++ coroutine 的基础知识，以及 asio::awaitable 作为 coroutine handler 的一些 idioms

后面有时间专门出一篇 post 介绍这个 idioms 好了

\#3

最近有点想先把 pre-commit hook 的脚本都“保存”到 repo 里，这样新环境设置 pre-commit hook 就一致化很多

所以打算抽个时间把这个 finalize 了再开始搞 https 支持的事情

---

好了这周就这样，下周见