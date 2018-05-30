---
title: Build Your Own Threadpool With C++
categories: PROGRAMMING
date: 2018-05-30 20:27:37
tags: [c++, thread-pool]
---
## Why Threadpool Matters

Why on the earth do we need thread-pool? The answer is obvious: for doing jobs behind the scenes.

That is, saying, you have a constant stream of incoming tasks to complete, and most of which either incur heavy computation or invovle device I/O, you definitely don't want to execute them on your main thread, because it will block your main thread until the job is done, making your application less responsive.

However, with thread-pool, you can simply submit a task to the pool, then continue what was doing; the task will eventually be completed on a thread of the thread-pool.

If your processor has multiple cores, the task is possibly performed concurrently with your jobs on the main thread.

## What Should a Threadpool Provide

Before we switch our focus to editor, we are better to think twice about what we can do with the thread-pool we will build.
<!-- more -->
### Specifying Pool Size

The number of running threads in the pool should be customizable to users, and this number usually depends on your available hardware cores and your current threading model.

So our TinyThreadPool should have a ctor that receives a size parameter.

```cpp
explicit TinyThreadPool(size_t num);
```

### Worker Thread Running Pattern

Essentially, the running pattern of worker threads is a simple producer-consumer model:
- the pool maintains a task-queue storing incoming tasks
- users are producers submiting a task to the queue
- worker threads are consumers that extract a task out of the queue and execute it

and of course, operations on the task-queue are thread-safe.

Each worker thread runs a loop, and it
1. blocks if the task-queue is empty
2. wakes up (if had went to sleep) and extracts a task to execute

For the task-queue, `std::deque<>` may not be the best, but neither will it be a bad choice; and for simplicity, we just ignore the case in which the task-queue is flooded by pending tasks, and simply leave it unbounded.

Synchronization operations on the task-queue can be done with `std::mutex` and `std::condition_variable`, which happens to be the foundation of classic blocking-queue implementation.

### Submitting Tasks

Our submitting task interface, `PostTask()`, accepts a callable object and its variadic arguments, and all of these are passed as forwarded via `std::forward()`.

Internally, we wrap the callable object in a `Task` instance, defined as:

```cpp
using Task = std::function<void()>;
```

and therefore, the task-queue is defined as:

```cpp
std::deque<Task> task_queue_;
```

To keep communication with submitted tasks, we use `std::future<>` as the intermedia, which brings us two more problems:
1. we need to figure out the return type of a task, which instantiates a `std::future<>` instance; and `std::result_of` is made for this.
2. we also need a way to create a future object; to keep implementation simple, we choose `std::packaged_task`

```cpp
template<typename F, typename... Args>
std::future<std::result_of_t<F(Args...)>> PostTask(F&& fn, Args&&... args)
{
    using R = std::result_of_t<F(Args...)>;

    // We have to manage the packaged_task with shared_ptr, because std::function<>
    // requires being copy-constructible and copy-assignable.
    auto task_fn = std::make_shared<std::packaged_task<R()>>(
        std::bind(std::forward<F>(fn), std::forward<Args>(args)...));

    auto future = task_fn->get_future();

    Task task([task_fn=std::move(task_fn)] { (*task_fn)(); });

    {
        std::lock_guard<std::mutex> lock(pool_mutex_);
        ENSURE(CHECK, running_).Require();
        task_queue_.push_back(std::move(task));
    }

    not_empty_.notify_one();

    return future;
}
```

Note the reason we use `shared_ptr` here.

### Teardown

When a thread-pool tears down, it is possible there still are tasks pending in the queue.

Shall we pause to wait until all pending tasks get completed, or just abandon them.

One compromise is that we introduce concept of shutdown-behavior, which can either be `BlockShutdown` or `SkipOnShutdown`.

If a task chooses to block during shutdown, then the teardown blocks until all tasks in that behavior complete, and any skip-on-shutdown tasks will be skipped.

Sure, thread-pool's dtor will wait for all worker threads to quit

## Usage At a Glance

```cpp
#include "tiny_thread_pool.h"

void Inc(int& i)
{
    ++i;
}

void PrintState(const char* msg, std::chrono::seconds delay)
{
    std::this_thread::sleep_for(delay);
    printf("%s\n", msg);
}

int main()
{
    using namespace std::chrono_literals;

    TinyThreadPool pool(2);

    pool.PostTask(&PrintState, "Prepare work 1", 2s);
    pool.PostTask(&PrintState, "Prepare work 2", 2s);

    pool.PostTaskWithShutdownBehavior(TaskShutdownBehavior::SkipOnShutdown, &PrintState, "Real work 1", 0s);
    pool.PostTaskWithShutdownBehavior(TaskShutdownBehavior::SkipOnShutdown, &PrintState, "Real work 2", 1s);

    pool.PostTask(&PrintState, "Real work 3", 2s);
    pool.PostTask(&PrintState, "Real work 4", 0s);

    pool.PostTaskWithShutdownBehavior(TaskShutdownBehavior::SkipOnShutdown, &PrintState, "Real work 5", 1s);

    return 0;
}
```

Full code can be found at [Eureka/TinyThreadPool](https://github.com/kingsamchen/Eureka/tree/master/TinyThreadPool)