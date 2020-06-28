---
title: 保持 ASIO io_context 无任务时运行
categories: PROGRAMMING
date: 2020-06-28 23:03:22
tags: [asio, io_context, work_guard]
---
ASIO 的 `io_context::run()` 如果发现没有 pending 的任务就会返回，对于 server 的监听线程来说这是符合常理的，因为无论如何至少有个 `acceptor::async_accept()` 在 pending。

但是如果想利用多核能力，那么一个常用的设计是：master 线程只负责 accept 进来的请求；将接受的 conn_sock 传递到 worker pool 里由其中一个 worker thread 处理。

每个 worker thread 其实也跑了一个 `io_context`，但是因为我们无法保证每个 worker 都有 pending task，所以如果不做特殊处理，`io_context::run()` 会直接返回，导致 worker thread 也退出，这和我们希望的背道而驰。

ASIO 中的 work guard 就是用了该解决这个问题：一个 work guard 能防止其关联的 io_context 在没有任务时退出。

```cpp
[[maybe_unused]] auto work_guard = asio::make_work_guard(io_ctx_);
io_ctx_.run();
```

1. `work_guard` 不影响 `io_context::stop()` 的功能和语义；如果需要结束 io_context loop，推荐的依然是使用这个方法
2. 如果向去掉 `work_guard` 的影响，使用 `work_guard::reset()` 即可；调用后 `io_context::run()` 会在没有 pending task 时返回

## Ref

1. [Prevent io_context::run from returning](https://dens.website/tutorials/cpp-asio/work)
