---
title: Object-Wide Cancellation and Per-Operation Cancellation in ASIO
categories: PROGRAMMING
date: 2026-03-15 16:35:20
tags: [cpp, asio, cancellation, asynchronous]
toc: true
---
In the post {% post_link general-purpose-cancellation-mechanism-stop-token std::stop_token: A General-Purpose Cancellation Mechanism %} we introduced how C++ handles cooperative cancellation since C++20.

However, Boost.Asio has its own cancellation mechanisms:
- object-wide cancellation support from the very beginning
- per-operation cancellation support since boost 1.77.0 / asio 1.20.0 (released in 2021)

## Why Cancellation is a Must for Boost.Asio

We all know there are two models of network I/O:

- the reactor model, represented by select/poll/epoll, in which we wait for read or write **readiness**, then perform the I/O operations ourselves
- the proactor model, represented by IOCP and io_uring, in which we submit a read or write operation with our user-provided buffer to the kernel, and wait for the **completion**

In reactor model, if we decide to cancel an operation, we can often just set a flag and remove the fd/socket from epoll's watch list.

Because we perform the I/O operations ourselves, we can check the flag and bail out early.

However, in proactor model, the IO operation is carried out by the kernel, and if the IO is not ready yet, the submitted operation is in the kernel's pending queue; and we cannot simply ask the kernel to check a user-provided flag.

So the kernel must provide some mechanism to allow us to cancel the pending operations without closing the fd/socket; such as `CancelIoEx()` for IOCP or `IORING_OP_ASYNC_CANCEL` for io_uring.

Boost.Asio is a proactor style event framework, as it must be compatible with IOCP, though it could use epoll on Linux. Nevertheless, in terms of the API design, Boost.Asio must offer a cancellation mechanism to allow us to cancel outstanding async operations.

## Object-Wide Cancellation

Some objects in Boost.Asio, like sockets or timers, offer a function `cancel()` which can cause **all** outstanding asynchronous operations to finish immediately with an operation-aborted error.

For a tcp/acceptor or timers, `cancel()` is enough in almost every use case; but a normal socket is able to perform a read and a write operation simultaneously and we may want to cancel the write only while leaving the read intact.

Moreover, object-wide cancellations are bound to an object, there is no way to cancel async operations that are not bound to an object, like, sorting a large array in a background worker thread.

That is why Boost.Asio introduced per-operation cancellation not long ago.

## Per-Operation Cancellation

`asio::cancellation_signal` and `asio::cancellation_slot` form a **one-to-one** signal/slot pair.

One-to-one means, if you produce more than one `cancellation_slot` out of a `cancellation_signal`, only the last one that is used would take effect

Moreover, if you want multiple async operations to be cancelled, you need the same number of cancellation signal/slot pairs, each pair corresponds to an async operation.

Here is a less-typical usage of this cancellation signal/slot pair:

```cpp
int main() {
    asio::cancellation_signal cancel_signal;

    std::thread j([slot = cancel_signal.slot()] mutable {
        std::atomic<bool> quit{false};
        slot.assign([&quit](asio::cancellation_type type) {
            SPDLOG_INFO("Cancel with type={} on thread={}",
                        static_cast<unsigned int>(type), std::this_thread::get_id());
            quit.store(true);
        });

        while (!quit.load()) {
            SPDLOG_INFO("Worker is doing job on thread={}", std::this_thread::get_id());
            std::this_thread::sleep_for(1s);
        }
    });

    std::this_thread::sleep_for(4s);
    SPDLOG_INFO("Emitting cancellation on thread={}", std::this_thread::get_id());
    cancel_signal.emit(asio::cancellation_type_t::all);

    j.join();
}

// [2026-03-14 22:42:59.506] [info] [main.cpp:113] Worker is doing job on thread=136068361877184
// [2026-03-14 22:43:00.507] [info] [main.cpp:113] Worker is doing job on thread=136068361877184
// [2026-03-14 22:43:01.507] [info] [main.cpp:113] Worker is doing job on thread=136068361877184
// [2026-03-14 22:43:02.508] [info] [main.cpp:113] Worker is doing job on thread=136068361877184
// [2026-03-14 22:43:03.507] [info] [main.cpp:119] Emitting cancellation on thread=136068436180608
// [2026-03-14 22:43:03.507] [info] [main.cpp:107] Cancel with type=4294967295 on thread=136068436180608
```

1. `cancellation_signal` is neither copyable nor movable
2. `cancellation_slot` provides no interface to query if a cancellation has been requested
3. the handler associated with a slot is invoked on the thread you called `emit()`, so you must account for thread safety yourself

I call this usage less-typical because cancellation signals and slots are usually used with Boost.Asio's async operations via `asio::bind_cancellation_slot()`:

```cpp
asio::io_context ioc{1};

asio::cancellation_signal cancel_signal;

struct worker_op {
    void operator()(std::error_code ec) {
        if (ec) {
            SPDLOG_INFO("{}", ec.message());
            return;
        }
        SPDLOG_INFO("Repeat worker is doing job");
        t.expires_after(1s);
        t.async_wait(asio::bind_cancellation_slot(cancel_slot, *this));
    }
    asio::steady_timer& t;
    asio::cancellation_slot cancel_slot;
};

asio::steady_timer repeat_worker(ioc);
worker_op op{repeat_worker, cancel_signal.slot()};
repeat_worker.async_wait(asio::bind_cancellation_slot(cancel_signal.slot(), op));

asio::steady_timer cancel_timer(ioc);
cancel_timer.expires_after(4s);
cancel_timer.async_wait([&cancel_signal](std::error_code ec) {
    (void)ec;
    cancel_signal.emit(asio::cancellation_type_t::all);
});

ioc.run();

// [2026-03-15 00:01:04.953] [info] [main.cpp:136] Repeat worker is doing job
// [2026-03-15 00:01:05.953] [info] [main.cpp:136] Repeat worker is doing job
// [2026-03-15 00:01:06.954] [info] [main.cpp:136] Repeat worker is doing job
// [2026-03-15 00:01:07.954] [info] [main.cpp:136] Repeat worker is doing job
// [2026-03-15 00:01:08.953] [info] [main.cpp:133] Operation canceled
```

An `asio::cancellation_slot` can be reused, but each outstanding async operation must explicitly be bound to that slot.

### Cancellation Types

Per-operation cancellation defines several cancellation types, depending on the side-effects of cancellation.

The full table is available at https://www.boost.org/doc/libs/latest/doc/html/boost_asio/reference/cancellation_type.html; and here are a few key observations:

> For example, if application logic requires that an operation complete with all-or-nothing side effects, it should emit only the total cancellation type. If this type is unsupported by the target operation, no cancellation will occur.

> Furthermore, a stronger guarantee always satisfies the requirements of a weaker guarantee. The partial guarantee still satisfies the terminal guarantee. The total guarantee satisfies both partial and terminal. This means that when an operation supports a given cancellation type as its strongest guarantee, it should honour cancellation requests for any of the weaker guarantees.

from https://www.boost.org/doc/libs/latest/doc/html/boost_asio/overview/core/cancellation.html

Usually, you can always emit a signal with `all`; while on the slot side, if the requested type is not supported, it can be omitted.

### Chaining Cancellations

For simple async operations, a single cancellation signal/slot pair is usually enough. However, Boost.Asio also provides `asio::cancellation_state` to allow us to chain several `asio::cancellation_slot` in async operation composition.

```cpp
template<typename CompletionToken>
auto async_low_wait(asio::steady_timer& timer, CompletionToken&& token) {
    return timer.async_wait(std::forward<CompletionToken>(token));
}

template<typename CompletionToken>
auto async_outer_wait(asio::steady_timer& timer, CompletionToken&& token) {
    auto op = [&timer, started = false](auto& self, boost::system::error_code ec = {}) mutable {
        if (!started) {
            started = true;

            auto parent_slot =
                    asio::get_associated_cancellation_slot(self);

            // Outer layer creates its own state from the caller's slot.
            auto cs = asio::cancellation_state(parent_slot);

            SPDLOG_INFO("[outer] started");

            // Pass the OUTER child slot down to middle.
            async_low_wait(
                    timer,
                    asio::bind_cancellation_slot(
                            cs.slot(),
                            std::move(self)));
            return;
        }

        if (ec == asio::error::operation_aborted) {
            SPDLOG_INFO("[outer] inner layer completed with cancellation");
        }

        self.complete(ec);
    };

    return asio::async_compose<CompletionToken, void(boost::system::error_code)>(
            std::move(op),
            token,
            timer);
}

int main() {
    asio::io_context io;

    asio::steady_timer work_timer(io);
    work_timer.expires_after(10s);

    asio::steady_timer cancel_timer(io);
    cancel_timer.expires_after(2s);

    asio::cancellation_signal sig;

    async_outer_wait(
            work_timer,
            asio::bind_cancellation_slot(
                    sig.slot(),
                    [](boost::system::error_code ec) {
                        std::cout << "[user handler] ec = " << ec.message() << "\n";
                    }));

    cancel_timer.async_wait(
            [&](boost::system::error_code ec) {
                if (!ec) {
                    std::cout << "[caller] emit terminal cancellation\n";
                    sig.emit(asio::cancellation_type::terminal);
                }
            });

    io.run();
}

// [2026-03-15 16:59:57.588] [info] [main.cpp:175] [outer] started
// [caller] emit terminal cancellation
// [2026-03-15 16:59:59.588] [info] [main.cpp:187] [outer] inner layer completed with cancellation
// [user handler] ec = Operation canceled
```

The key idea of using `asio::cancellation_state` is that:

1. create a `asio::cancellation_state` instance out of the parent slot
2. `cs.slot()` returns the **child slot** whose cancellation state reflects the state of the parent slot.

## Higher-Level Cancellation Abstractions

Boost.Asio also offers higher level abstractions built on top of cancellation signal/slot and state.

If they satisfy your needs, you should prefer them first.

### Parallel Group

I would not go into detail here, as we have a better choice, unless you are limited to toolchains pre-C++20.

You can refer to https://www.boost.org/doc/libs/latest/doc/html/boost_asio/overview/composition/parallel_group.html

and official examples: https://www.boost.org/doc/libs/latest/doc/html/boost_asio/examples/cpp14_examples.html#boost_asio.examples.cpp14_examples.parallel_groups

### Overloaded && and || for Coroutines

Boost.Asio overloads `&&` and `||` for its `awaitable<>` types

So you can simply:

- use `co_await (coro_fn1 && coro_fn2 && ...)` to wait until all awaitable-functions complete successfully, or any of them fails with an exception, then the others are cancelled immediately
- use `co_await (coro_fn1 || coro_fn2 || ...)` to wait until any awaitable-function has completed successfully, and the others are cancelled immediately

Both `&&` and `||` supports short-circuit semantics.

See https://www.boost.org/doc/libs/latest/doc/html/boost_asio/overview/composition/cpp20_coroutines.html#boost_asio.overview.composition.cpp20_coroutines.co_ordinating_parallel_coroutines for further details.

This feature, though still experimental, is quite elegant.

Furthermore, each Boost.Asio coroutine has its own `asio::cancellation_state`, derived from the cancellation state established by `asio::co_spawn()`.

```cpp
int main() {
    asio::io_context ioc{1};
    asio::cancellation_signal cancel_signal;

    auto launch = []() -> asio::awaitable<void> {
        SPDLOG_INFO("Launch work timer");
        co_await []() -> asio::awaitable<void> {
            auto cs = co_await asio::this_coro::cancellation_state;

            SPDLOG_INFO("Work timer starts");
            asio::steady_timer timer(co_await asio::this_coro::executor);
            timer.expires_after(10s);
            auto [ec] = co_await timer.async_wait(
                    asio::bind_cancellation_slot(cs.slot(), asio::as_tuple));
            if (ec) {
                SPDLOG_INFO("Work timer {}", ec.message());
                co_return;
            }
            SPDLOG_INFO("Timer tick");
        }();
    };
    asio::co_spawn(ioc, launch, asio::bind_cancellation_slot(cancel_signal.slot(), asio::detached));

    asio::steady_timer cancel_timer(ioc);
    cancel_timer.expires_after(2s);
    cancel_timer.async_wait([&cancel_signal](boost::system::error_code ec) {
        if (!ec) {
            SPDLOG_INFO("Emit cancellation");
            cancel_signal.emit(asio::cancellation_type::all);
        }
    });

    ioc.run();
}

// [2026-03-15 21:19:31.118] [info] [main.cpp:234] Launch work timer
// [2026-03-15 21:19:31.118] [info] [main.cpp:238] Work timer starts
// [2026-03-15 21:19:33.118] [info] [main.cpp:256] Emit cancellation
// [2026-03-15 21:19:33.118] [info] [main.cpp:244] Work timer Operation canceled
```