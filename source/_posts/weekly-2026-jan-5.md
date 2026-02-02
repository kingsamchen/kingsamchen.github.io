---
title: 一周杂记 in Week 5 Jan 2026
categories: CODE-LIFE
date: 2026-02-01 23:37:26
tags: [杂记]
---
本周（01/26 ~ 02/01）是一月份第五周，1月份的最后一周。

## Life

\#1

周二下午抓住个机会找了 US 的大经理 1:1，不过没有一上来就说老崔不行，而是从两个刚遇到的“事故”入手，先试探一下大经理对老崔的看法。

整体聊下来的感官是：大经理算是一个比较中立理性的人，但是之前被老崔信息差攻击的太厉害了

另外是老崔之前瞎 push 确实是拿到了一部分尚方宝剑，说是一部分是因为上面的 goal 其实只有A，但是老崔硬要逞能直接扩大到 ABC

至于这次 1:1 能起多大作用另说把，反正我能做的都做了。老崔再瞎搞几下把机会霍霍玩，最后就算他滚球了我们也没有第三次机会了。

周四傍晚大部门的年会和聚餐，东西还可以，比之前年会的纯预制菜好吃一些。

抽奖环节顺带中了一个小奖，星巴克的水杯。

晚上因为要赶紧回去带娃所以7点半提前开溜了。

周五是规划好的部门内的团建活动，上午在会议室搞了一个小小的破冰活动，中午吃完饭就开车带着几个同事去了富阳的一个别墅。

然后就是比较流程的团体团建活动，运气还好，分的小组并列一等奖拿了个玩偶。

晚上提早吃完饭之后就开着同事回来了，不过因为还下着雨而且遇到周五的下班高峰，回程花了一个多小时，到家都7点半多了。

\#2

周三中午趁着 WFH 在健身房过了一个佳明推荐的训练，不过太久没上强度了所以这个 6分配10分钟切换5分配20分钟切换6分配十分钟的 训练直接给我折腾的不轻。

中间5分配每十分钟歇了一次，不然心率真的扛不住，第二次5分配跑完的时候心率都冲上180了。

\#3

周四上午带着娃去社区打疫苗+体检+抽血，整体非常顺利。

而且也是带老婆产检常去的蒋村卫生院，停车也方便。一套弄完到家都才10:30。

不过这次娃打针倒是哭的比较凶，需要哄才能停下来，估计是长大了有一些想法了。

周六傍晚和媳妇儿一起开车去东站接我妈，又到了2个月带娃轮换的时间。

\#4

这周继续看 Daredevil: Born Again

周三电影俱乐部看了

- **欣德·拉贾布之声 صوت هند رجب** 3/5 聚焦之前巴以冲突的，不如搞成那种伪纪录片 表演痕迹太出戏了


## Work

\#1

**Asynchronous Programming with C++ | 6. Promise and Futures**

- basic usages of std::promise and std::future and std::future::share() would turn the future into a shared_future (the original one is invalidated)
- std::packaged_task wraps promise/future inside but it also supports reset() for reuse/re-invocation.
- 手搓的 cancellation 和 fork-join，感觉确实有点累人，尤其是对于 non-trivial tasks
- 一个简单的手搓的带 continuation 的 future_task，核心就是每个 task 自己带一个 promise 以及一组 dependent (shared) futures，自己执行的时候依赖的 futures 要先执行才行；思路上和常见的 then() 的因该是反过来，后者是当前 future 执行之后如果有后继就驱动后继 task

**CppNow 2025 | How to Build a Flexible Robot Brain One Bit at a Time - Ramon Perez** https://www.youtube.com/watch?v=akJznI1eBxo&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=56

- 讲的比较散
- 提了 python 和 modern cpp 很多写法都很类似
- 一些硬件架构和他们 menlo lab 在搞的 cortex tools
- 整体上算是 introduction 性质的 talk

**CppNow 2025 | Zngur - Simplified Rust/C++ Integration - David Sankel** https://www.youtube.com/watch?v=k_sp5wvoEVM&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=55

- 可以让 cpp 调用 rust 代码的一套方案
- 另外看这 talk，Adobe 后面应该会逐渐往 Rust 倾斜？

**C++ Weekly - Ep 516 - C++26's User Generated static_assert Messages** https://www.youtube.com/watch?v=CmfgZa-bcTg

- 核心点一句话概括就是 https://en.cppreference.com/w/cpp/language/static_assert.html：

    ```cpp
    static_assert( bool-constexpr , constant-expression )	(3)	(since C++26)

    #if __cpp_static_assert >= 202306L
    // Not real C++ yet (std::format should be constexpr to work):
    static_assert(sizeof(int) == 4, std::format("Expected 4, got {}", sizeof(int)));
    #endif
    ```


**C++ Weekly - Ep 204 - Christmas Class 2019 - Chapter 5 of 5 - Lambdas To The Limits** https://www.youtube.com/watch?v=TKPDW--AzWo

- 列了一些常见的 lambda 的问题
- 不过只是汇总没有 Q&A

**C++ Weekly - Ep 203 - Christmas Class 2019 - Chapter 4 of 5 - The Evolution of Lambdas** https://www.youtube.com/watch?v=tpGPVrTudLA

- 核心：writing a lambda **inside a fold expression** can generate **a lot more assembly** than defining the lambda once **outside** the fold. The key idea: when the lambda is *inside* the fold, it effectively gets **instantiated repeatedly** (and you can end up with multiple copies of similar code), which can lead to **code bloat**

**C++ Weekly - Ep 202 - Christmas Class 2019 - Chapter 3 of 5 - Utilizing Lambdas** https://www.youtube.com/watch?v=VEqOOKU8RjQ

- 如何只用 C++98 实现一些常用的 lambda 功能…

**C++ Weekly - Ep 201 - Christmas Class 2019 - Chapter 2 of 5 - Building On Lambdas** https://www.youtube.com/watch?v=DolVujl_EUw

- 用 c++ 98 模拟 lambda 甚至 high order function

**Quickly checking that a string belongs to a small set** https://lemire.me/blog/2022/12/30/quickly-checking-that-a-string-belongs-to-a-small-set/

- 疯狂优化的技巧：把几个前缀字符串转换成对应的数字，然后hash后直接查表
- 问了 GPT 才理解 shiftxor_table 这个表是精心构造的：文里的 shiftxor_table 其实就是一个**“8 字节为一槽（slot）的小型完美哈希表”：先把候选字符串都当成 NUL 结尾/补零到 8 字节的 uint64_t，然后用一个很便宜的 hash（两次右移再 xor，再用 mask）算出“槽位偏移”，把对应的 8 字节字符串直接放进那个槽**。查询时同样算偏移、读回 8 字节、和输入的 uint64_t 做一次比较即可。文中给出的就是最终“编译好”的表和 hash 参数

\#2

这周给 fawkes 的 middleware 做了纯古法编程的 coroutine 支持，现在 middleware handler 可以是普通函数也可以是 coroutine

PR：https://github.com/kingsamchen/fawkes/pull/21

不过在做这个过程中踩了好几个意外的坑，下面展开说说。

\#3

fawkes 给 middlewares 做 coroutine 支持，第一个版本出现了 unittest 失败的case，一开始以为 fold expression 和 coroutine 不兼容，但是 simple case 又可以

最后发现意思编译器的 bug：只要 ignore unused 就会触发 short circuit 失效

```cpp
template<bool IsForward, std::size_t... I, is_middleware... Mws, typename F>
asio::awaitable<middleware_result> apply_middlewares_impl(std::tuple<Mws...>& middlewares,
                                                          std::index_sequence<I...> idx_seq,
                                                          request& req,
                                                          response& resp,
                                                          F&& fn) {
    constexpr auto N = idx_seq.size();
    auto result = middleware_result::proceed;
    // Short circuit if possible.
    auto&& f = std::forward<F>(fn);
    if constexpr (IsForward) {
        // [[maybe_unused]] auto _ = ((co_await f(std::get<I>(middlewares), req, resp, result)) && ...);
        boost::ignore_unused(((co_await f(std::get<I>(middlewares), req, resp, result)) && ...));
        // SPDLOG_INFO("ok={}", ok);
    } else {
        bool ok =
            ((co_await f(std::get<N - I - 1>(middlewares), req, resp, result)) && ...);
        SPDLOG_INFO("ok={}", ok);
    }
    co_return result;
}
```

上面使用了 `boost::ignore_unused` 也会导致所有 middlewares 都被处理了一遍，即使中间有 middleware 返回 false 要 abort 也还是会继续。

看起来就像是 short circuit 失效了。

但是如果用 `(void)` 或者 `static_cast<void>` 就没事，而且会有告警

另外在这个问题上，AI真的派不上用场，他们胡诌了一段理由说 coroutine 会导致 fold expression short circuit 失效，并且给的 fix 就是把 fold expression 换成递归模板

\#4

第二个问题是做了一个 run awaitable sync 的辅助 ut 的东西，踩到了 gcc-12 对于 std::future/promise 的 regression，导致在 github ci 上直接失败了

这个问题倒是问的 GPT 和 Gemini https://gemini.google.com/share/991a8c940409

最后直接换成不用 future 的实现… 实现参考最后的PR

\#5

然后踩了第三个坑：`std::optional<T>::value()` 依然会触发 **bugprone-unchecked-optional-access** 这个 tidy 告警…

我寻思我都用 `value()` 了肯定是依赖没值时候抛异常的这个行为了，为啥还要强制我检查？

官方的原因也挺无语的…

> The answer is that we assume most users do not realize the difference between `value()` and `operator*()`

https://clang.llvm.org/extra/clang-tidy/checks/bugprone/unchecked-optional-access.html#access-with-operator-vs-value

研究了一下发现从 22 或者 23 开始（主要是没找到22 的 web docs）这个 check 支持  **IgnoreValueCalls** 关闭对 `value()` 的检查

但是因为目前主要版本还是 21，所以直接先加 NOLINT 过去再说

---
