---
title: std::function Must be Copyable
categories: PROGRAMMING
date: 2018-04-23 23:55:09
tags: [c++, std::function, packaged_task, thread-pool]
---
前几天在写一个 [ThreadPool 的轮子](https://github.com/kingsamchen/Eureka/tree/master/TinyThreadPool)的时候，碰到一个问题：无论我用什么办法，都不能将 `std::packaged_task` 传递到 `std::function` 中，编译器始终提示访问了 `std::packaged_task` 被删除的拷贝构造函数。

在这个点卡了许久，未果，翻了一下 cpp reference，突然发现一个细节：`std::function` 必须是支持复制的，顿时茅塞顿开。

`std::function` 包裹的函数对象必须足够轻量，使得 pass-by-value 的 context 下不会出现明显的大量开销，因此这个类型要求指语义是符合逻辑的。

而 `std::packaged_task` 不是值语义，仅支持移动语义，因此无法作为 `std::function` 的一部分。

最后的解决方案是引入一个 `std::shared_ptr` 作为中间层，通过 lambda 保存这个 `std::shared_ptr`。

附注：本来打算开一个 post 来简单讲解一下如何从头实现一个简单可用的 thread-pool，但是因为懒癌发作，代码写完了也就不想动手写文字了。后面看看是不是有机会可以补上。