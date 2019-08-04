---
title: Monthly Read Posts in July 2019
categories: PROGRAMMING
date: 2019-08-04 10:55:30
tags: [sync.pool, quadratic residues, reader-writer lock]
---

## Programming Languages

[Go: Understand the Design of Sync.Pool](https://medium.com/@blanchon.vincent/go-understand-the-design-of-sync-pool-2dde3024e277)

Insights:

1. package 的 `init()` 函数中注册 runtime GC 的回调，以清理池子中的数据

2. 会为每个 P 创建一个 `pool` 实例；每个实例包含 _private_ 和 _shared_ 两部分。

    其中 private 只能由 P 自己访问，因此无需考虑并发安全；shared 能被其他的 P 访问，需要考虑并发安全。
    
3. `Get()` 的操作顺序：private area -> shared area -> create via New()

4. `Put()` 会尝试先把 item 放回 private area，如果 private 没有 item；否则就放到 shared area。

---

[A pattern for overcoming non-determinism of Golang select statement](https://medium.com/@pedram.esmaeeli/a-pattern-for-overcoming-non-determinism-of-golang-select-statement-139dbe93db98)

文章里说的 non-determinism 指的是在一个 `select` statement 中监听多个 channel，channel的触发顺序是非确定性的。

这点应该算是一个 well-known background。

至于作者的做法，通过提供一个 interface ，把多个不同类型的 channel 给合并成了一个...

## Algorithms

[How to Generate a Sequence of Unique Random Integers](https://preshing.com/20121224/how-to-generate-a-sequence-of-unique-random-integers/)

利用了 quadratic residues 的一个性质：

1. x^2 mod p is unique as long 2x < p
2. 在（1）基础上，p - x^2 mod p is unique, as long p mod 4 = 3

在此基础上设计一个 one-to-one 的排列函数 f；并且只要 f 的复合函数仍然满足 one-to-one，也仍然能够保持前面的 unique 性质。

BTW：说起来后面应该抽个时间补一下数学和算法了...

## Concurrency

[How Do Locks Lock?](http://www.moserware.com/2008/09/how-do-locks-lock.html)

.NET 中 reader-writer lock 的一些内部实现。

.NET 的 rwlock 支持所谓的 upgrade/promotion，将一个 reader-lock 提升为 writer lock。

## Misc

[The Skills Poor Programmers Lack](https://justinmeiners.github.io/the-skills-programmers-lack/)

1. understand the working details
2. anticipate problems
3. learn to design and architect