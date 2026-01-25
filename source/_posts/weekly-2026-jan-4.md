---
title: 一周杂记 in Week 4 Jan 2026
categories: CODE-LIFE
date: 2026-01-25 22:53:15
tags: [杂记]
---
本周（1/19 ~ 1/25）是1月份第四周，一月份已经进入了下旬。

## Life

\#1

先说公司的破事吧。

周末老崔强行部署上版本，然后周六周日连续两天都搞出了不同的问题，周日的问题一直到晚上都没解决，到了半夜我还被拉进 warroom 帮他们查问题...

周一早上醒来发现卧槽怎么 warroom 还在开呢，问了K指导说是US那边也介入之后现在扯皮到底是回滚还是强行刷数据。

于是周一基本在看戏，不过看了半天开始报告说其他部门的服务因为依赖我们也出问题了，所以最后只能回滚。

有点意思

另外这周部门US那边的大经理也过来了，所以刚来就在脸上炸了一个大的。

然而并没有什么卵用，周三下午复盘会的时候我直接冲塔说这次部署过于 rush；结果是大经理自己承担了责任，而且言语中透露出对老崔的 leadearship 肯定。

那特么还能说啥，算了，等时间被🔪呗。

不过你还别说，周中传出说小规模 layoff 了一些人，然后听说补偿是 N+4+年终，所以约等于是 N+6；而无固定期限合同的老员工就是直接 2N。

想了一下真不如把我刀了，比起来肯定比后面被老崔PIP只拿N+1收益高啊。哎

哦，周日还被蔡司令和chao一起拉去陪着大经理爬山，还好整体难度还行，毕竟之前锻炼的强度也足够了。

就是爬完一身汗，衣服都湿了...

\#2

section 2 说点好的吧，周一年会中了一个还行的奖，中了一个亚朵深睡枕，不过到家体验之后发现并不适合我，因为这个枕头过高，所以仰睡有点难受，难绷。

周五晚上约了keting一起吃饭，在楼下等了他半小时，他被大K拉着开会，开着开着开始讲我们组的情况…

吃饭的时候和 keting 讲了好多，keting 也给了一些建议，不过现在公司的环境是在太烂了，而且我现在确实有点想提前退休的想法了。

这周二大雪，上一次这么大雪应该是18年了。

秋宝第一次看到下雪，还是很激动的。给秋宝拍了不少照片。

\#3

周六上午开车送老婆去查房，中午一起吃了老头儿

就点了四个菜，结账时候看了一下卧槽居然200多块钱...

不过味道确实不错，和老婆直接吃饱了。

## Work

\#1

**Asynchronous Programming with C++ | 13. Improving Asynchronous Software Performance**

- show how to use built-in clock to measure speed
- how to use googlebenchmark
- how to use perf
- false sharing
- memory cache
- 一个利用 cached head/tail 的 reduce sharing 的 SPSC 优化版

**CppCon 2023 | Back to Basics: Initialization in C++ - Ben Saks** https://www.youtube.com/watch?v=_23qmZtDBxg&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=21

- c++ 初始化基础，直接用 chatgpt 总结了一下
- Prefer `T{}` when you mean “value-initialize / zero-ish default state” and want to dodge vexing-parse footguns.
- Use `{}` when you *want* narrowing to be rejected; use `()` when you *don’t* want `initializer_list` to steal overload resolution.
- When designing types, be cautious with `std::initializer_list` constructors and with changes that affect “aggregate-ness,” because they change how users can initialize your type.
- 另外 C++20 可以直接用 explicit(false) 来表明就是需要 implicit conversion

**CppCon 2023 | Back to Basics: C++ Concurrency - David Olsen** https://www.youtube.com/watch?v=8rEGu20Uw4g&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=22

- 节奏很快内容很多不过基本也都是入门教学型
- 优势是涵盖面广，包含了C++20引入的那几个同步原语

**CppCon 2023 | Optimizing Away C++ Virtual Functions May Be Pointless - Shachar Shemesh** https://www.youtube.com/watch?v=i5MAXAxp_Tw&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=23

- 这个 talk 有点意思：大部分业务场景下 virtual fn 并不会比普通函数有显著的性能下降，二者波动差异完全在 margin error 内；而 benchmarking 在这个范围很多时候都不靠谱，有的 bench 甚至虚函数更快
- 建议就是，跟着你的设计需求走，只有当 performance critical 的时候再考虑不用虚函数，并且一定要 measure 之后

**CppCon 2023 | Lock-free Atomic Shared Pointers Without a Split Reference Count? It Can Be Done! - Daniel Anderson** https://www.youtube.com/watch?v=lNPZV9Iqo3U&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=20

- 一个目前偏研究向的的 real atomic shared_ptr 的方案，不过看评论说 gcc 新版本已经采用类似方案在特定场合优化
- 方案和去年一个 talk 讲的 split/double word不同，是引入一个 control block 的 ref-counted 搭配 RCU/hazard ptr 来做管理
- benchmark 在 warm cache 时候表现不错；另外如果没有真的有很强需求，直接用 folly 的实现好了…

**C++ Weekly - Ep 515 - Revolutionize Your Templates with static_assert of non-value-dependent Exprs** https://www.youtube.com/watch?v=pwf45vaXm3Q

- 新的编译器（例如 gcc-13）开始支持把 statc_assert 的编译再也不是全局的，意味着可以放到 if..constexpr 的分支里用
- 不过 Jason 也提到了优先考虑 `=delete` 对应函数是不是一个更好的做法

**C++ Weekly - Ep 514 - C++26 on 1990 DOS?** https://www.youtube.com/watch?v=dtO94ifh7Ac

- 一个叫 DJGPP https://www.delorie.com/djgpp/ 的项目致力于给 DOS 支持比较新的 GNU 开发套件的项目，包括比较新版本的 gcc
- 然后 Jason 一顿操作也没成功

**C++ Weekly - Ep 207 - C++20's jthread and stop_token** https://www.youtube.com/watch?v=-yaz4n5zWZs

- std::jthread 除了析构时 auto-join 之外，Jason 认为最亮眼的地方是运行函数支持 std::stop_token

**C++ Weekly - Ep 206 - Surprising Conversions with CTAD** https://www.youtube.com/watch?v=IhjvznH9GvI

- 核心是 std::tuple有一个从 std::pair&& 来的隐式转换，然后又恰好给这个转换构造加一个 CTAD 推导规则导致“无意间”可能会从 std::pair 创建一个 tuple
- Jason 认为这个行为并不好

**C++ Weekly - Ep 205 - Christmas Class 2019 - Chapter 5 of 5 Answers** https://www.youtube.com/watch?v=i2HyDHAVBsk

- 估计是前面几期 Jason 搞得一些 quiz 的后续答案

**Compile-Time Strings** https://accu.org/journals/overload/30/172/wu/

截至2022年12月，compile-time std::string 并不好用，所以有一些特定场合得 compile-time solutions

- 直接用 char_traits 或者 string_view 来获取 length
- 用 char_traits 或者 string_view 来查找 character
- 用 std::string_view 来做 string comparison，语义完美契合
- std::string_view 无法强制汇编生成 sub-string，试了一下最新的编译器和23，确实也不行；只能用 array 搭配模板参数来倒一下，因为 array size 是强 compile-time args

    ```cpp
    template <size_t Count>
    constexpr auto copy_str(const char* str)
    {
      array<char, Count + 1> result{};
      for (size_t i = 0; i < Count; ++i)
      {
        result[i] = str[i];
      }
      return result;
    }
    ```

- 用 passing lambda 来实现 passing argument in comile-time

    ```cpp
      #define CARG typename
      #define CARG_WRAP(x) [] { return (x); }
      #define CARG_UNWRAP(x) (x)()

      template <CARG Int>
      constexpr auto make_constant(Int cn)
      {
        constexpr int n = CARG_UNWRAP(cn);
        return integral_constant<int, n>{};
      }

      auto result = make_constant(CARG_WRAP(2));
      static_assert(
       std::is_same_v<integral_constant<int, 2>,
       decltype(result)>);
    ```

- compile-time string in C++ 20

    ```cpp

    template <size_t N>
    struct compile_time_string {
      constexpr compile_time_string(
        const char (&str)[N])
      {
        copy_n(str, N, value);
      }
      char value[N]{};
    };
    template <compile_time_string cts>
    constexpr auto operator""_cts()
    {
      return cts;
    }

    template <compile_time_string cts>
    struct cts_wrapper {
      static constexpr compile_time_string str{cts};
    };

    auto a = cts_wrapper<"Hi">{};
    auto b = cts_wrapper<"Hi">{};
    static_assert(
      is_same_v<decltype(a), decltype(b)>);
    ```

\#2

这周突发奇想，在公司利用 Cursor 搭配 Opus 模型 vibe coding 了一段，给 fawkes 的 middleware 加上了 coroutine 支持。

实话说，opus 确实有点厉害，生成出来的变更确实能用，而且我 prompt 给的并不多，整体上看确实是一个比较能依赖的实习生。

不过仔细看的话会发现生成的代码非常不优雅，很多细节都不讲究，甚至一开始直接用的 recursive template 处理的 tuple，而我原来的实现直接用的 fold expression，这个还需要我单独给他 complain 它才会重新改一版。

后来想了一下，还是决定后面古法手作写一遍，然后两边来对比一下

---

好了这周就这样
