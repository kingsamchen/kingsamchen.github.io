---
title: Monthly Read Posts in June 2017
categories: PROGRAMMING
date: 2017-07-02 14:43:56
tags: [c++, move safety, stack, heap, mix-in, connection management, tuple]
---
[一种头像缓存策略](https://github.com/nixzhu/dev-blog/blob/master/2015-10-08-navi.md)

本来还以为有什么惊天地的策略...这不是很普通的策略嘛...

[Move Safety](http://foonathan.net/blog/2016/07/23/move-safety.html)

这篇 post 引入了一个 move-safety 的概念；和 exception safety 类似，move safety 描述了一个实例被移动之后的 post-condition invariant.

Why do we need move safety? Because `std::move()` creates artificial temporaries, and we might want to use them again after the move operation.

4-Level Move Safety
- No move guarantee: copy only
- Strong move safety: moved object is valid and well-defined; like std::unique_ptr
- Basic move safety: moved object is valid, but its state is unspecified; like std::string, due to possible SSO.It is what the standard library guarantees for all types unless otherwise specified.
- No move safety: the state is invalid, and you can only call its destructor, or assign it a new value.

[Stack and Heap](https://blog.tartanllama.xyz/c++/2016/07/18/stack-and-heap/)

这篇 post 挺有意思。

作者观点是，无论 *heap* 还是 *stack* 来描述一个 C++ object 的 storage location 都是不准确的，因为标准并未规定 storage location 具体是什么，而规定了所谓的 storage duration：**Storage duration defines the minimum potential lifetime of the storage that contains the object**.

4 standard-defined storage duration:
- Static storage duration
- Automatic storage duration
- Thread storage duration
- Dynamic storage duration

[Mix-in Based Programming in C++](http://www.drdobbs.com/cpp/mixin-based-programming-in-c/184404445)
[Paper: Mixed based programming in C++](https://yanniss.github.io/practical-fmtd.pdf)

第一篇是介绍 mix-in 的 post，第二篇是在 mix-in 基础上提出一种 mix-in layer 的 paper。

mix-in 是一个挺有意思的 patter，但是因为没有自身语言支持，在 C++ 里的实现有时候会遇到比较 tricky 的问题。

第一篇 post 就提到了一个核心问题：如何有效地解决 mix-in classes 的 construction problem。但是讲真，post 里给出的 solution 有点诡异过头了；如果现实中的工程问题需要这样的一个解决方案，弃用 mix-in 弄不好是一个更靠谱的做法。

至于第二篇 paper，其实核心所谓的 mix-in layer，概括起来就是

```c++
template <class Next >
class NUMBER : public Next {
public:
    class Workspace : public Next::Workspace {
        // Workspace role members
    }；
    class Vertex : public Next::Vertex {
        // Vertex role members
    };
};
```

paper 中还提到了对工程中使用 mix-in 的一些建议和 recommended practice，看看也还不错。

[Connection Management in Chromium](https://insouciant.org/tech/connection-management-in-chromium/)

Chromium 官方研发写的 post。

提到了他们试图解决 connection latency & parallelism 的问题。

于是就有这么几个点：

Handshakes (including TCP handshake & SSL handshake) are costly. Therefore, a better connection management is on demand.

Optimizations on transport layer (TCP or SPDY)

.etc

[Apply tuple to function efficiently](https://www.preney.ca/paul/archives/486)

Yet another implementation of apply-on-tuple
