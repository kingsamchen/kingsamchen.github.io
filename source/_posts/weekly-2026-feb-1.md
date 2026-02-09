---
title: 一周杂记 in Week 1 Feb 2026
categories: CODE-LIFE
date: 2026-02-08 22:07:15
tags: [杂记]
---
这周（02/02 ~ 02/08）是2月份第一周，下周是节前最后一个工作周了。

## Life

\#1

周一的时候老崔私聊我说约了周三下午谈绩效，因为已经知道了当初老崔给我打的初始绩效和他的一些扣分点，所以我提前都做了准备，本来是打算逐一抨击之后找US的大经理S“聊一聊”。

结果没想到老崔最后时刻学 Trump TACO 了，给了我一个正常的绩效，而且从头到尾没提到之前在+1和hr面前抨击我的点。

估计是之前出的那个RCA影响太大了，而且事前我当众提醒 E2E 有相关失败可能有可能，所以老崔强行给我按头打那个绩效确实太欲盖弥彰了。

估计他也怕我真的去找S冲他

不过怎么说呢，这个利好不改既定结局。只要S手里没人，还得依赖老崔给他做事，26年还得是我在这团队的最后一年。除非有变数能限制掉老崔

不过蔡司令 transfer 的时间定了，这个算是个大利好，虽然他自己说是逃难跑到 US，但是只要存有机会，翻盘的理论可能还是有的。

毕竟当初确实也没想到老张真能下课啊。

\#2

因为秋宝还太小，所以今年春节就不回去了。给家里人买了票，让他们春节过来。

家里初四做东，所以他们初三下午回去就行，然后初六祠堂的事情结束我妈又要再来接着带娃。

不过老婆觉得春节呆在杭州实在是太无聊了

之前21年在杭州呆满了一个春节，确实挺无聊的

\#3

这周把 Daredevil Born Again S01 看完了，周日陪老婆看了阿凡达3。老婆看了一半中间直接睡着了 🤣

- **阿凡达：火与烬 Avatar: Fire and Ash‎ (2025) 3/5** 没有传的那么差 整体商业片的水准还是有的 就是实在太长了 这个长度只做单纯的商业片对于卡梅隆来说就太失身份了
- **夜魔侠：重生 第一季 Daredevil: Born Again Season 1‎ (2025)** 质感上还是差Netflix的一点。另外不能因为这不是罚叔的个人剧就把人写的那么降智，人罚叔的风格这群anti-vigilant task force能直接里外里杀穿三次😑

## Work

\#1

**CppNow 2025 | std::optional — Standardizing Optionals over References - A Case Study - Steve Downey** https://www.youtube.com/watch?v=cSOzD78yQV4&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=53

- 核心内容是 std::optional<T&> 标准化的一些故事
- tips: 引用版本是特化，內部存的其实还是指针；但是有海量的语义相关的 corner cases

**C++ Weekly - Ep 517 - Tool Spotlight: ClangBuildAnalyzer** https://www.youtube.com/watch?v=gEQ5_FjCihA

- clang `-ftime-trace` 可以利用 https://github.com/aras-p/ClangBuildAnalyzer 做进一步的聚合分析，按照整个 build 的耗时进行输出
- 另外一个是 https://github.com/jlfwong/speedscope 可以用来查看各类火焰图

**C++ Weekly - Ep 200 - Christmas Class 2019 - Chapter 1 of 5 - Understanding Lambdas** https://www.youtube.com/watch?v=3hGSlUGEXtA

- Jason 的 Christmas 特辑的首篇
- 这次是讲 mutable lambda 做 Fibonacci

**C++ Weekly - Ep 199 - [[nodiscard]] Constructors And Their Uses** https://www.youtube.com/watch?v=E_ROB_xUQQQ

- C++ 20 开始 `[[nodiscard]]` 可以修饰构造函数，作用是为了避免经典的 `std::lock_guard(mutex)` 这种压根没锁上的问题

**C++ Weekly - Ep - 198 - Surprise Uses For `explicit` Constructors** https://www.youtube.com/watch?v=Q4SXFkTzD28

- 任意 constructor 都可以标记为 explicit，包括 default constructor
- 标记之后 `{}` 或者 `{42, 42}` 这种构造都会失败，需要手动加上类名

**C++ Weekly - Ep 197 - 25 Years of Best Practices** https://www.youtube.com/watch?v=ayIJ4b6z-7g

- 探索 25 年前 OS/2 上 Borland C++ 对于 RVO/NRVO 的优化
- 基本是考古系列

**Determining if a template specialization exists** https://www.lukas-barth.net/blog/checking-if-specialized/

- 检查是否存在函数模板特化：直接利用 SFINAE + std::declval

    ```cpp
      template <class T, class Dummy = decltype(std::swap<T>(std::declval<T &>(),
                                                             std::declval<T &>()))>
    ```

    但是如果想做 generic 的实现就会更麻烦一些

    ```cpp
    auto betterL = [](auto &lhs, auto &rhs) -> decltype(std::swap(lhs, rhs)) {
      return std::swap(lhs, rhs);
    };
    constexpr bool sometype_has_swap =
        std::is_invocable_v<decltype(betterL), SomeType &, SomeType &>;

    ```

- 检查是否存在类模板特化：初始方案依赖 default ctor，也即直接尝试构造一个对象 `decltype(T{})`
    - 改进方案是拿析构的返回值，标准规定这部分是 void

        ```cpp
          template <class... Args,
                    class dummy = decltype(std::declval<Tmpl<Args...>>().~Tmpl<Args...>())>
        ```


    这个方案有个坑是如果特化是某个 TU 中间部分引入或这个只有某个TU才有，那么会导致 odr-violation，整个程序是 ill-formed

    另外这个方案在 MSVC 下有一些问题，MSVC 对于其他类型始终会定义，但是没有提供 operator()

\#2

这周抽了点时间，做了 fawkes 和 gin 两个维度的 benchmark，目的其实就是做一个心里有数，后面也有一个比较 compelling reason 说不应该花那么大代价用 golang 重写 2 million LoC 的业务代码

测试核心是两个点：

- 一个是简单字符串的返回，主要是想压一下框架的极限
- 一个是随机wait [10ms, 50ms]，主要是模拟业务停等在网络请求上，这个显然不是CPU bound，也符合大部分 rest api servers 的情况

测试环境是 ubuntu 2404，8个CPU core 用 taskset 只绑定0-3，wrk 绑定 4-7，避免影响

```cpp
namespace asio = boost::asio;
namespace http = boost::beast::http;

DEFINE_uint32(port, 7890, "Port number to listen on");

namespace {

int tls_rnd() {
    thread_local std::mt19937 eng{std::random_device{}()};
    thread_local std::uniform_int_distribution<int> dist{10, 50}; // NOLINT(*-magic-numbers)
    return dist(eng);
}

} // namespace

int main(int argc, char* argv[]) {
    gflags::ParseCommandLineFlags(&argc, &argv, true);
    spdlog::cfg::load_env_levels();

    try {
        asio::io_context ioc{1};
        fawkes::io_thread_pool io_pool(4);

        fawkes::server server(ioc, io_pool);

        server.do_get("/status",
                      [](const fawkes::request&, fawkes::response& resp) -> asio::awaitable<void> {
                          resp.text(http::status::ok, std::string{"hello world"});
                          co_return;
                      });

        server.do_get("/delayed",
                      [](const fawkes::request&, fawkes::response& resp) -> asio::awaitable<void> {
                          asio::steady_timer timer(co_await asio::this_coro::executor);
                          timer.expires_after(std::chrono::milliseconds(tls_rnd()));
                          co_await timer.async_wait();
                          resp.text(http::status::ok, std::string{"hello world"});
                          co_return;
                      });

        server.listen_and_serve("0.0.0.0", static_cast<std::uint16_t>(FLAGS_port));
        SPDLOG_INFO("Server is listening at {}", FLAGS_port);

        ioc.run();
    } catch (const std::exception& ex) {
        SPDLOG_ERROR("Unexpected error: {}", ex.what());
    }

    return 0;
}
```

对应的 gin

```golang
package main

import (
        "io"
        "net/http"
        "runtime"
        "time"

        "github.com/gin-gonic/gin"
		"github.com/valyala/fastrand"
)

func main() {
        runtime.GOMAXPROCS(4)
        r := gin.New()

        r.GET("/status", func(c *gin.Context) {
                c.String(http.StatusOK, "hello world")
        })

        r.GET("/delayed", func(c *gin.Context) {
                delay := time.Duration(fastrand.Uint32n(41)+10) * time.Millisecond
                time.Sleep(delay)
                c.String(http.StatusOK, "hello world")
        })

        r.Run(":9876")
}
```

用 fastrand 包是因为标准库的 math.rand 存在全局锁竞争，不过实测下来这个差别对 RPS 延迟 吞吐 啥的几乎没有肉眼可见区别

`/status` 的 bench 结果大概这样

```
#
# fawkes(cpp)
#

$ taskset -c 4-7 wrk -t 4 -c 100 -d 30s http://127.0.0.1:7890/status
Running 30s test @ http://127.0.0.1:7890/status
  4 threads and 100 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   118.49us   88.97us   8.52ms   90.55%
    Req/Sec   195.58k     8.21k  211.33k    80.77%
  23366131 requests in 30.10s, 2.20GB read
Requests/sec: 776297.81
Transfer/sec:     74.77MB

---

$ taskset -c 4-7 wrk -t 4 -c 200 -d 30s http://127.0.0.1:7890/status
Running 30s test @ http://127.0.0.1:7890/status
  4 threads and 200 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   235.08us  121.81us   5.72ms   91.36%
    Req/Sec   202.29k     5.53k  230.88k    77.08%
  24152657 requests in 30.02s, 2.27GB read
Requests/sec: 804512.83
Transfer/sec:     77.49MB

---

$ taskset -c 4-7 wrk -t 4 -c 400 -d 30s http://127.0.0.1:7890/status
Running 30s test @ http://127.0.0.1:7890/status
  4 threads and 400 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   488.18us  153.83us  11.47ms   87.24%
    Req/Sec   197.11k     4.52k  223.46k    79.33%
  23538202 requests in 30.04s, 2.21GB read
Requests/sec: 783437.58
Transfer/sec:     75.46MB

---

$ taskset -c 4-7 wrk -t 4 -c 600 -d 30s http://127.0.0.1:7890/status
Running 30s test @ http://127.0.0.1:7890/status
  4 threads and 600 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   769.46us  201.83us  17.32ms   89.10%
    Req/Sec   189.78k    18.84k  510.99k    98.83%
  22509621 requests in 29.85s, 2.12GB read
Requests/sec: 753979.66
Transfer/sec:     72.62MB

---

$ taskset -c 4-7 wrk -t 4 -c 800 -d 30s http://127.0.0.1:7890/status
Running 30s test @ http://127.0.0.1:7890/status
  4 threads and 800 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     1.04ms  250.08us  18.75ms   91.22%
    Req/Sec   186.44k     3.85k  205.60k    76.92%
  22260003 requests in 30.05s, 2.09GB read
Requests/sec: 740848.80
Transfer/sec:     71.36MB

#
# gin(golang)
#

$ taskset -c 4-7 wrk -t 4 -c 200 -d 30s http://127.0.0.1:9876/status
Running 30s test @ http://127.0.0.1:9876/status
  4 threads and 200 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     1.00ms    1.15ms  16.07ms   85.53%
    Req/Sec    72.32k     4.67k   86.64k    68.75%
  8634517 requests in 30.03s, 1.03GB read
Requests/sec: 287563.51
Transfer/sec:     35.10MB

---

$ taskset -c 4-7 wrk -t 4 -c 400 -d 30s http://127.0.0.1:9876/status
Running 30s test @ http://127.0.0.1:9876/status
  4 threads and 400 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     1.45ms    1.49ms  17.41ms   85.75%
    Req/Sec    89.29k     5.88k  109.06k    68.83%
  10660721 requests in 30.04s, 1.27GB read
Requests/sec: 354847.17
Transfer/sec:     43.32MB

---

$ taskset -c 4-7 wrk -t 4 -c 600 -d 30s http://127.0.0.1:9876/status
Running 30s test @ http://127.0.0.1:9876/status
  4 threads and 600 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     1.88ms    1.67ms  24.00ms   82.51%
    Req/Sec    94.39k     5.66k  110.18k    69.58%
  11269424 requests in 30.06s, 1.34GB read
Requests/sec: 374877.28
Transfer/sec:     45.76MB

---

$ taskset -c 4-7 wrk -t 4 -c 800 -d 30s http://127.0.0.1:9876/status
Running 30s test @ http://127.0.0.1:9876/status
  4 threads and 800 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     2.50ms    2.00ms  26.56ms   81.53%
    Req/Sec    91.06k     5.54k  105.63k    69.42%
  10874122 requests in 30.06s, 1.30GB read
Requests/sec: 361805.33
Transfer/sec:     44.17MB
```

同等压力下 fawkes 的 rps 和吞吐基本是 gin 的两倍，而且延迟比较稳定

`/delayed` 的 bench 大概这样

```
#
# fawkes(cpp)
#

$ taskset -c 4-7 wrk -t 4 -c 4000 -d 30s http://127.0.0.1:7890/delayed
Running 30s test @ http://127.0.0.1:7890/delayed
  4 threads and 4000 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency    30.47ms   11.83ms  57.44ms   56.88%
    Req/Sec    32.95k   435.03    33.90k    82.17%
  3933987 requests in 30.08s, 378.93MB read
Requests/sec: 130799.34
Transfer/sec:     12.60MB

$ taskset -c 4-7 wrk -t 4 -c 10000 -d 30s http://127.0.0.1:7890/delayed
Running 30s test @ http://127.0.0.1:7890/delayed
  4 threads and 10000 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency    38.32ms   12.39ms 255.57ms   60.27%
    Req/Sec    64.70k     3.68k   69.53k    94.23%
  7698731 requests in 30.01s, 741.55MB read
Requests/sec: 256528.10
Transfer/sec:     24.71MB

$ taskset -c 4-7 wrk -t 4 -c 15000 -d 30s http://127.0.0.1:7890/delayed
Running 30s test @ http://127.0.0.1:7890/delayed
  4 threads and 15000 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency    70.70ms   15.38ms 119.88ms   68.80%
    Req/Sec    52.31k     2.21k   84.44k    88.36%
  6216137 requests in 30.10s, 598.75MB read
Requests/sec: 206526.70
Transfer/sec:     19.89MB

$ taskset -c 4-7 wrk -t 4 -c 20000 -d 30s http://127.0.0.1:7890/delayed
Running 30s test @ http://127.0.0.1:7890/delayed
  4 threads and 20000 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   100.47ms   19.71ms 167.23ms   68.67%
    Req/Sec    49.89k     4.17k   83.19k    95.11%
  5788293 requests in 30.09s, 557.53MB read
Requests/sec: 192347.32
Transfer/sec:     18.53MB

#
# gin(golang)
#

$ taskset -c 4-7 wrk -t 4 -c 2000 -d 30s http://127.0.0.1:9876/delayed
Running 30s test @ http://127.0.0.1:9876/delayed
  4 threads and 2000 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency    30.34ms   11.85ms  62.91ms   57.53%
    Req/Sec    16.55k   296.03    17.29k    83.08%
  1975861 requests in 30.07s, 241.19MB read
Requests/sec:  65709.10
Transfer/sec:      8.02MB

$ taskset -c 4-7 wrk -t 4 -c 4000 -d 30s http://127.0.0.1:9876/delayed
Running 30s test @ http://127.0.0.1:9876/delayed
  4 threads and 4000 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency    32.01ms   12.43ms 108.75ms   60.21%
    Req/Sec    31.39k     1.25k   33.94k    64.33%
  3747334 requests in 30.07s, 457.44MB read
Requests/sec: 124615.33
Transfer/sec:     15.21MB

$ taskset -c 4-7 wrk -t 4 -c 10000 -d 30s http://127.0.0.1:9876/delayed
Running 30s test @ http://127.0.0.1:9876/delayed
  4 threads and 10000 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency    66.93ms   36.72ms 309.80ms   60.65%
    Req/Sec    37.42k     3.87k   45.22k    73.83%
  4454003 requests in 30.04s, 543.70MB read
Requests/sec: 148282.24
Transfer/sec:     18.10MB

$ taskset -c 4-7 wrk -t 4 -c 15000 -d 30s http://127.0.0.1:9876/delayed
Running 30s test @ http://127.0.0.1:9876/delayed
  4 threads and 15000 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   100.38ms   65.44ms 261.84ms   53.35%
    Req/Sec    37.58k     4.30k   89.10k    80.80%
  4459974 requests in 30.09s, 544.43MB read
Requests/sec: 148214.09
Transfer/sec:     18.09MB

$ taskset -c 4-7 wrk -t 4 -c 20000 -d 30s http://127.0.0.1:9876/delayed
Running 30s test @ http://127.0.0.1:9876/delayed
  4 threads and 20000 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   128.96ms   91.54ms 358.05ms   49.71%
    Req/Sec    38.63k     4.47k   46.42k    82.36%
  4489456 requests in 30.08s, 548.03MB read
Requests/sec: 149267.64
Transfer/sec:     18.22MB
```

低负载（压力没打满）的情况下，fawkes 只比 gin 好一点点；但是压力上来之后 fawkes 比 gin rps 要好 50% ~ 100%，并且延迟只有后者的 50% ~ 70%

基于这个结果，fawkes 只要后续没有太坑的实现，性能下限是绝对有保证的。因为我们的目标压根就不是做什么性能最好的 rest server，而是业务开发足够友好，性能上不拖后腿，能顺利替换掉之前基于 httpd 的那托实现。

\#3

fawkes 做了 expected 100-continue 的支持，顺带显式处理了一下对端 connection reset 的错误，即不认为是 unhandled/unexpected

wrk 测试结束后会利用 RST 来避免进入 TIME_WAIT

PR 见：https://github.com/kingsamchen/fawkes/pull/22

---

好了这周就这样，下周见
