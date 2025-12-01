---
title: Using ASIO awaitable the Easy Way
categories: PROGRAMMING
date: 2025-11-29 20:03:50
tags: [cpp, asio, coroutine, awaitable]
---
## 基础规则

先复习一下，基础规则是针对所有 C++ coroutine 实现的，不单单对 ASIO 的实现成立

1. A function becomes a coroutine the moment you suspend from within its body by invoking `co_await` or `co_yield` and `co_return`.
2. If you call a coroutine `c` (remember, coroutines are just functions) from a function `f`, the **function `f` is *not* a coroutine unless you `co_await` the result of `c`**.

    In other words, calling a coroutine isn’t sufficient to make the caller a coroutine.


## awaitable<T>

`asio::awaitable<T>` 设计上是 lazy evaluation 也即

- initial suspend 会直接 suspend
- coroutine frame 需要在 co_await 的时候才会触发执行

因为 stackless coroutine 的传染特性，只有在 coroutine 中才能 co_await 另一个 coroutine，所以 ASIO 的设计中 coroutine chain 的最顶层会有一个 `asio::co_spawn()` 来创建一个 coroutine frame/context。

如果是在 main() 中，那么大致上长这样：

```cpp
asio::awaitable<void> async_foo() {
    // do sth...
    co_return;
}

int main() {
    asio::io_context io_ctx{1};

    asio::co_spawn(io_ctx, launcher(), asio::detached);

    io_ctx.run();
    return 0;
}
```

## Order of Execution

总结基础规则和 asio::awaitable 的特性可知：

- 仅当 coroutine `C1` co_await coroutine `C2` 的 return-object/awaiable type 时，C2 的 coroutine frame/body 才会开始执行

### 案例1

```cpp
namespace {

asio::awaitable<int> make_awaitable(int& i) {
    SPDLOG_INFO("In make_awaitable");
    //++i;
    co_return 42;
}

asio::awaitable<void> intermediate() {
    SPDLOG_INFO("Before lambda");
    auto res = []() -> asio::awaitable<int> {    // lambda is not a coroutine
        int i = 42;
        SPDLOG_INFO("Before make_awaitable");
        auto r = make_awaitable(i);
        SPDLOG_INFO("After make_awaitable");
        return r;
    }();
    SPDLOG_INFO("After lambda");
    auto n = co_await std::move(res);
}

asio::awaitable<void> launcher() {
    SPDLOG_INFO("Before intermediate");
    auto r = intermediate();
    SPDLOG_INFO("After intermediate");
    co_await std::move(r);
    co_return;
}

} // namespace

int main() {
    asio::io_context io_ctx{1};

    asio::co_spawn(io_ctx, launcher(), asio::detached);

    io_ctx.run();
    return 0;
}
```

Output

```log
[2025-11-04 00:09:14.570] [info] [main.cpp:26] Before intermediate
[2025-11-04 00:09:14.571] [info] [main.cpp:28] After intermediate
[2025-11-04 00:09:14.571] [info] [main.cpp:13] Before lambda
[2025-11-04 00:09:14.571] [info] [main.cpp:16] Before make_awaitable
[2025-11-04 00:09:14.571] [info] [main.cpp:18] After make_awaitable
[2025-11-04 00:09:14.571] [info] [main.cpp:21] After lambda
[2025-11-04 00:09:14.571] [info] [main.cpp:7] In make_awaitable
```

1. 因为 `intermediate()` 自身是个 coroutine，所以需要 co_await 他的 return object 才会执行 coroutine frame；所以 Before intermediate 和 After intermediate 先执行
2. `intermediate()` 函数里的这个 lambda 虽然返回类型是 awaitable<int>，但是 lambda 自身并不是一个 coroutine，因为他没有 co_await/co_yield/co_return；所以 lambda 直接早于 After lambda 执行
3. `make_awaitable()` 是一个 coroutine 所以他的 coroutine frame 要在 co_await std::move(res) 之后开始执行

### 案例2

把上面的代码稍作调整

```diff
asio::awaitable<void> intermediate() {
    SPDLOG_INFO("Before lambda");
    auto res = []() -> asio::awaitable<int> {    // lambda is not a coroutine
        int i = 42;
        SPDLOG_INFO("Before make_awaitable");
        auto r = make_awaitable(i);
        SPDLOG_INFO("After make_awaitable");
-        return r;
+        co_return 42;
    }();
    SPDLOG_INFO("After lambda");
    auto n = co_await std::move(res);
}
```

直接把 lambda 变成了 coroutine，并且 `res` 对应成了 lambda 这个 coroutine 的 return object

所以 lambda 会直到 `intermediate()` 函数最后 co_await 的时候才会执行，这部分顺序就变成了

```log
[2025-11-04 00:13:03.486] [info] [main.cpp:13] Before lambda
[2025-11-04 00:13:03.486] [info] [main.cpp:21] After lambda
[2025-11-04 00:13:03.486] [info] [main.cpp:16] Before make_awaitable
[2025-11-04 00:13:03.487] [info] [main.cpp:18] After make_awaitable
```

另外需要注意的是， `make_awaitable()` 这个 return object 直接变成了 unused，所以对应的 coroutine 就压根不会执行