---
title: ASIO Buffer 使用简记
categories: PROGRAMMING
date: 2019-09-22 20:27:48
tags: [ASIO, networking, buffer]
---

近段有相当一部分时间在[熟悉和练习 ASIO](https://github.com/kingsamchen/Eureka/tree/master/learn-asio)。

练习过程中发现 ASIO 中如何使用&管理 buffer 是新手大概率会遇到的问题。

结合最近几个 practice demo，稍微简单总结了一下使用经验：

**0x00**

`const_buffer` 和 `mutable_buffer` 是两个 fundamental buffer classes。二者的区别在语义上表达的很明显了。

实现上二者提供的接口非常一致，除了一个面向 `const void*`，另一个面向 `void*`。这点可以从 ctor 和 `data()` 中看出。

另外，为了和 C++ 现有的 const cast semantics 保持一致，一个 `mutable_buffer` 对象可以 implicitly converted to `const_buffer`。

<!-- more -->

IO 操作上，`const_buffer` 通常对应一个 write operation，而 `mutable_buffer` 通常对应一个 read operation。



**0x01**

`asio::buffer()` 可以根据用户提供的 buffer 创建一个 `const_buffer` 或 `mutable_buffer`。

并且因为上面提到的 buffer-constness semantics，哪怕此时创建出来的是 `mutable_buffer`，也能自动转换到目标需要的 `const_buffer`。

所以大部分场合下，只需要使用 `asio::buffer()` 构建 IO buffer。



**0x02**

上面说的 IO buffer 非常像 `std::string_view`，它们只 refer 某一块现有的内存，不会获得内存的 ownership。

因此，使用者必须保证用作 buffer 的内存的生命周期在整个 IO 操作期间都有效。

这是刚使用 ASIO 时很容易犯的一个错误。

对于有 Windows IOCP 编程经验的人来说，这点很容易理解，因为异步 IO 必须事先提供好内存，而 ASIO 采纳的刚好也是 proactor model。



**0x03**

对于 `asio::async_read_until()`，配套的 buffer 是 [dynamic buffer](http://think-async.com/Asio/asio-1.12.2/doc/asio/reference/DynamicBuffer.html)。

因为这个函数需要读到某个 `delim` 或者 `pred` 满足才会返回，因此 buffer 大小无法实现确定。

_dynamic buffer_ 的要求之一就是支持 _**memory storage can be automatically resized as required**_

另，满足 dynamic buffer 的前提条件是：需要是一个 const buffer 或者 mutable buffer。

使用上，`std::string` 和 `std::vector<char>` 可以直接作为底层存储使用。

`asio::dyanmic_buffer()` 可以直接创建 dynamic buffer class。



**0x04**

如果接受了上面 dynamic buffer 的设定，那么一定要注意，对于普通的 const buffer 或者 mutable buffer，如果使用 `asio::buffer()` 构造时不显式指定大小，那么内部会直接使用底层存储的 `size()` 作为大小。

如果底层存储为 empty，那么会导致每次 IO 操作提供的 buffer 为空，会导致 IO 操作立即返回，且 `std::error_code` 没有任何错误。

这里和使用 dyanmic buffer 不同。

我在实现基于 ASIO 的 [socks4a proxy](https://github.com/kingsamchen/Eureka/tree/master/learn-asio/socks4a) 时就被这个细节坑了一下。