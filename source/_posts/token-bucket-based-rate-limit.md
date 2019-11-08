---
title: 一个轮子：基于 token bucket 的 rate-limit
categories: PROGRAMMING
date: 2019-11-04 20:31:14
tags: [algorithm, token bucket, rate-limit]
---
地址：https://github.com/kingsamchen/Eureka/tree/master/token-bucket-rate-limit

基于 [token bucket](https://en.wikipedia.org/wiki/Token_bucket) 的限流实现相比于简单的“时间窗口计数”，更加“平滑”和稳定，不容易出现两个时间窗口衔接前后的 burst 请求。

并且可以直接用到上传/下载的速率控制上。

对于后端服务，可以用作实例的单机请求控制。

这里不打算详细介绍这个机制算法，具体的 concept/idea 可以看 Wikipedia，只对一些实现细节做一点小小的 hint。

虽然概念上，会以一定的速率 `rate` 往桶里填充 token，但是实际实现上，直接算出两次访问桶的时间差，然后乘以 `rate` 就能得到过去这段时间要往桶里填充多少 token 了。

所以这里有一个核心的过程：调整桶在任意时刻 `t` 时桶里的可用 token 数。

另外，考虑到扩展性，可以引入另外一个填充 token 的周期概念：tick。

即：

- 每个 tick 往桶里填充 N 个 token（quantum）
- 一个物理时间间隔 d（单位可以是秒，分，毫秒 .etc）为一个 tick 周期

这样填充速率就不受限为秒级速率了。

注意：10 tokens per 100ms 和 100 tokens per 1s 是有区别的。前者填充桶的间隔更短，可以减少请求等待的时间间隔。

桶的容量（capacity）如果**明显大于**填充速率 quantum，那么容量可以用来支持某段时间内的 burst 请求，这可能是期望的也可能不是期望的。

另外一个需要注意的点是，桶的可用 token 数是否必须严格大于0。

如果是，那么对于从桶取走 token 的接口语义需要仔细思考和设计（如果多个请求上下文共用一个桶，那么很可能会出现某个上下文一直拿不到 token 的情况）。

FYI：这个 demo 是基于 CPP 实现的，因为工作中一直在用 golang，为了避免 CPP 生疏，还是有必要经常性的练习。