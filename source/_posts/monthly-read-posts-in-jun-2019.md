---
title: Monthly Read Posts in June 2019
categories: PROGRAMMING
date: 2019-07-21 14:09:48
tags: [memory model, golang]
---
## Concurrency

[Memory Barriers Are Like Source Control Operations]( https://preshing.com/20120710/memory-barriers-are-like-source-control-operations/)

用 VCS 来类比四种 memory fence

---

[Acquire and Release Semantics](https://preshing.com/20120913/acquire-and-release-semantics/)

C++ provides acquire and release semantics, which are in fact founded on barriers.

acquire semantcis: `#LoadLoad` and `#LoadStore`; apply to read operations.

Acquire semantics prevent memory reordering of the read-acquire with any read or write operation that follows it in program order.

release sematncis: `#StoreStore` and `#LoadStore`; apply to write operations.

Release semantics prevent memory reordering of the write-release with any read or write operation that precedes it in program order.

---

[Weak vs. Strong Memory Models](https://preshing.com/20120930/weak-vs-strong-memory-models/)

Categories of memory models.

---

[This Is Why They Call It a Weakly-Ordered CPU](https://preshing.com/20121019/this-is-why-they-call-it-a-weakly-ordered-cpu/)

Memory reordering in ARM cpus

## Programming Languages

[golang nil 101](https://go101.org/article/nil.html)

屎坑子真多。

---

[why-i-dont-like-go.html](https://www.teamten.com/lawrence/writings/why-i-dont-like-go.html)

深有感触...

而且居然还是2016年的文章
