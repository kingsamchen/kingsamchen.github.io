---
title: "std::stop_token: A General-Purpose Cancellation Mechanism"
categories: PROGRAMMING
date: 2026-02-20 11:29:12
tags: [cpp, stop_token, cancellation, asynchronous]
toc: true
---
## Using std::stop_source and std::stop_token

C++20 introduces `std::stop_source` and `std::stop_token` as a mechanism for general-purpose cooperative cancellation.

The key concept is: these two work very much like subject/observer in the _observer pattern_

- a `std::stop_source` is the subject of a stop-state
- one or more `std::stop_token` associated with a `std::stop_source` are observers/views of that stop-state, i.e. one-to-many mapping

besides:

- `std::stop_token` is a **thread-safe** observer/view of the associated stop-state, so it is safe for a `std::stop_token` on a thread different from the thread using `stop_source`; and it is also safe to have several `std::stop_token`s on varied threads
- a non-empty `std::stop_token` can be produced from a non-empty `stop_source` by calling `stop_source::get_token()`
- `std::stop_token` is cheap to copy, and all copies share the associated stop-state

The code below is a typical example of using stop_source & stop_token to signal the worker thread to quit:

```cpp
#include <cassert>
#include <chrono>
#include <stop_token>
#include <thread>

#include <fmt/format.h>

int main() {
    using namespace std::chrono_literals;

    std::stop_source stop_source; // 1) define the stop-state source

    std::thread th([stop_signal = stop_source.get_token()] { // 2) acquire a stop_token of the source
        for (; not stop_signal.stop_requested();) {          // 3) observe that if the source has requested stop
            fmt::println("worker thread is doing job");
            std::this_thread::sleep_for(1s);
        }
        fmt::println("worker is requested to stop");
    });

    std::this_thread::sleep_for(4s);

    fmt::println("requesting the worker to stop");
    bool first_requested = stop_source.request_stop(); // 4) request to stop and notify the change to all tokens.
    assert(first_requested == true);

    th.join();

    return 0;
}
```

>💡 A `std::stop_source::request_stop()` on an already stop-requested `std::stop_source` is a no-op, and the function returns `true` only if it is the first call.

## Using std::jthread

`std::jthread` is also introduced in C++20.

Besides auto-joining on destruction, a `std::jthread` also carries a `std::stop_source`, and it is designed to allow you to use stop_source to request the `std::jthread` to stop.

We can adapt the previous sample to use `std::jthread`:

```cpp
#include <cassert>
#include <chrono>
#include <stop_token>
#include <thread>

#include <fmt/format.h>

int main() {
    using namespace std::chrono_literals;

    std::jthread th([](std::stop_token stop_signal) { // 1) jthread will pass into an associated stop_token
        for (; not stop_signal.stop_requested();) {   // 2) observe that if the source has requested stop
            fmt::println("worker thread is doing job");
            std::this_thread::sleep_for(1s);
        }
        fmt::println("worker is requested to stop");
    });

    std::this_thread::sleep_for(4s);

    fmt::println("requesting the worker to stop");
    bool first_requested = th.request_stop(); // 3) request to stop and notify the change to all tokens.
    assert(first_requested == true);

    th.join();

    return 0;
}
```

If the function you pass to a `std::jthread` takes a `std::stop_token` as its **first** argument, the `std::jthread` would automatically pass an associated `std::stop_token` to the function.

## Using std::stop_callback

If a `std::stop_token` is a read-only observer of a `std::stop_source`, then a `std::stop_callback` is an observer with a custom action; indeed, it looks more like an observer in traditional viewpoint of the design pattern.

```cpp
template<class Callback>
class stop_callback {
public:
    template<class C>
    explicit stop_callback(const stop_token& st, C&& cb);

    ~stop_callback();  // Unregisters callback
};
```

`std::stop_callback` registers a callback function to be invoked when the associated `std::stop_token` is requested; and it offers several key properties:

- **Type erasure**: The callback function is type erased, each `std::stop_callback<F>` can store a different callable type; and CTAD makes passing callback even more convenient
- **RAII semantics**: Constructor registers the callback and destructor unregisters the callback
- **Immediate invocation**: If stop was already requested, the callback is invoked in constructor
- **Thread safety**: Callbacks are invoked synchronously on the thread which called `std::stop_source::request_stop()`, provided they are registered before the stop-request; and callback registration is thread-safe

However, `std::stop_callback` itself is neither copyable nor movable.

```cpp
int main() {
    using namespace std::chrono_literals;

    std::jthread th([](std::stop_token stop_signal) {
        std::stop_callback on_stop_requested(stop_signal, [] {
            fmt::println("worker is requested to stop; tid={}", std::this_thread::get_id());
        });

        for (; not stop_signal.stop_requested();) {
            fmt::println("worker thread is doing job; tid={}", std::this_thread::get_id());
            std::this_thread::sleep_for(1s);
        }

        fmt::println("worker is about to quit; tid={}", std::this_thread::get_id());
    });

    std::this_thread::sleep_for(4s);

    fmt::println("requesting the worker to stop; tid={}", std::this_thread::get_id());
    bool first_requested = th.request_stop();
    assert(first_requested == true);

    th.join();

    return 0;
}
```

Output of one run on my computer:

```text
worker thread is doing job; tid=34768
worker thread is doing job; tid=34768
worker thread is doing job; tid=34768
worker thread is doing job; tid=34768
requesting the worker to stop; tid=51240
worker is requested to stop; tid=51240
worker is about to quit; tid=34768
```

Given its amazing properties, Vinnie Falco, the author of the Boost.Beast library, argues that it, in combination with stop_token, is essentially [a one-shot signaling mechanism](https://www.vinniefalco.com/p/recognizing-stop_token-as-a-general).

## std::stop_source is Also Copyable

Copying from a `std::stop_source` will make the copy's associated stop-state the same as the original stop-source.

Although it is a relatively rare use case, we can use this property to derive some sort of first-win-cancels-others implementation:

```cpp
int main() {
    using namespace std::chrono_literals;

    std::stop_source orig_stop_src;

    std::vector<std::thread> thds;
    for (int i = 0; i < 2; ++i) {
        thds.emplace_back([my_stop_src = orig_stop_src, id = i]() mutable {
            std::mt19937 rnd(std::random_device{}());
            std::uniform_int_distribution<> gen(2, 5);
            int c = 0;
            int max_jobs = 3 + gen(rnd);
            for (; not my_stop_src.stop_requested() && c < max_jobs; ++c) {
                fmt::println("worker-{} thread is doing job", id);
                std::this_thread::sleep_for(1s);
            }

            if (c == max_jobs) {
                fmt::println("worker-{} has done all its job, now quit", id);
                my_stop_src.request_stop();
            } else {
                fmt::println("worker-{} is requested to quit by the other worker", id);
            }
        });
    }

    thds[0].join();
    thds[1].join();

    return 0;
}
```

Two workers share one associated stop-state, and whoever completes first will cancel the other.

## Advantages Over Using Plain Atomic Flags

The biggest advantage of using `std::stop_token` is that the `std::stop_token` is **recognized by the standard library** as the general-purpose cancellation mechanism, so the library would continue to adopt it for cancellation.

`std::condition_variable_any::wait()` supports being woken up via `std::stop_token` since C++ 20, for which you can't with just atomic flags:

```cpp
int flag = 0;

std::mutex mtx;
std::condition_variable_any cond;

void waits(std::stop_token st) {
    fmt::println("waiter is waiting");
    std::unique_lock lock(mtx);
    cond.wait(lock, st, [] { return flag == 1; });
    fmt::println("waiter stops waiting with flag={}", flag);
}

void signal(std::stop_source& stop) {
    fmt::println("signaler is busy now");
    std::this_thread::sleep_for(3s);
    fmt::println("signaling for timeout");
    stop.request_stop();
}

int main() {
    std::stop_source stop_src;
    std::thread t1(waits, stop_src.get_token()), t2(signal, std::ref(stop_src));
    t1.join();
    t2.join();

    return 0;
}
```

So, in future standards, the standard library will feature using `std::stop_token` more heavily, building a more complete ecosystem.

For example, `std::execution` in C++26 also uses `std::stop_token` [for cancellation](https://en.cppreference.com/w/cpp/experimental/execution.html).
