---
title: The Lambda Coroutine Fiasco
categories: PROGRAMMING
date: 2026-01-10 14:56:48
tags: [cpp, lambda, coroutine]
---
This is a follow-up to {% post_link using-asio-awaitable Using ASIO awaitable the Easy Way %}.

The CppCoreGuidelines include a [rule](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#cp51-do-not-use-capturing-lambdas-that-are-coroutines) that says: "_Do not use capturing lambdas that are coroutines_"; and if not disabled in your `.clang-tidy` file, both clangd & clang-tidy will warn the use of capturing lambda coroutines.

Since `std::future<>` doesn't support coroutine yet, we can adapt the guidelines' sample code with `asio::awaitable<>` instead, to reproduce and then dive into the use-after-free memory access issue caused by a lambda coroutine.

```cpp
struct Tracker { // NOLINT(cppcoreguidelines-special-member-functions)
    int val;
    explicit Tracker(int v) : val(v) {}
    ~Tracker() {
        SPDLOG_INFO("~Tracker");
    }
};

int format_as(const Tracker& tracker) {
    return tracker.val;
}

Tracker get_value() {
    return Tracker(42);
}

asio::awaitable<void> capture_lambda() {
    auto value = get_value();
    asio::awaitable<void> co;
    {
        auto lambda = [value]() -> asio::awaitable<void> {
            co_await asio::this_coro::executor;
            SPDLOG_INFO("the value={}", value);
            // "value" has already been destroyed
        };
        co = lambda();
    } // the lambda closure object has now gone out of scope
    SPDLOG_INFO("about to co_await");
    co_await std::move(co);
}

int main() {
    asio::io_context io_ctx{1};
    asio::co_spawn(io_ctx, capture_lambda(), asio::detached);
    io_ctx.run();
    return 0;
}
```

Auxiliary class `Tracker` here is used as an indicator of destruction of lambda closure; and when we compile and run the executable, we have following output:

```log
$ ./coro_awaitable
[2026-01-10 20:19:28.075] [info] [main.cpp:35] ~Tracker
[2026-01-10 20:19:28.075] [info] [main.cpp:58] about to co_await
[2026-01-10 20:19:28.075] [info] [main.cpp:53] the value=42
[2026-01-10 20:19:28.075] [info] [main.cpp:35] ~Tracker
```

When the lambda goes out of scope, the `Tracker` instance the lambda captured gets destructed, therefore the subsequent access to its value, after the lambda coroutine is resumed, is a use-after-free access.

So why wasn't the capture "moved-into" the coroutine frame?

The answer is simple: it was, but was _**reference-captured**_.

The coroutine lambda is compiled or transformed equivalently to

```cpp
auto lambda_operator_call(const lambda* this) -> asio::awaitable<void> {
    co_await some_awaitable;
    // access this->value
}
```

It's pretty much like a coroutine with passed-by-reference parameters issue, which is also discouraged by [CppCoreGuidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#cp53-parameters-to-coroutines-should-not-be-passed-by-reference).

## Solutions

CppCoreGuidelines offers two solutions:

- Pass as arguments instead of using captures
- Simply use a normal function coroutine rather than a lambda coroutine

Either ensures arguments the coroutine demands on are alive in the coroutine by moving them into coroutine frame.

If C++23 is available to you, you can use deducing-this pattern to enforce pass-lambda-by-value:

```cpp
auto lambda = [value](this auto) -> asio::awaitable<void> {
    co_await asio::this_coro::executor;
    SPDLOG_INFO("the value={}", value);
    // "value" has already been destroyed
};
```

=>

```cpp
auto lambda_operator_call(this auto) -> asio::awaitable<void>;
```

=>

```cpp
auto lambda_operator_call(lambda) -> asio::awaitable<void>;
```

so the captures are also moved into coroutine frame.

---

Bonus tip: the crux of this issue is the lambda goes out of scope, so if you don't pass the awaitable out of lambda's scope, it's totally fine for this capturing lambda coroutine.

## What about ASAN

In fact, AddressSanitizer is able to detect this memory issue, but with a couple of caveats.

First, if the value is allocated on the stack, like an `int`, or a SSO short `std::string`, you must turn on the **stack-use-after-return** check by

```shell
export ASAN_OPTIONS=detect_stack_use_after_return=1
```

and compile your executable in Clang, because GCC most of the time is unable to catch this kind of issue.

Second, if the value is allocated on the heap, no extra effort is required, and either GCC or Clang is able to catch the memory issue.
