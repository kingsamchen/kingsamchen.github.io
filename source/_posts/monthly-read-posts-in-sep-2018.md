---
title: Monthly Read Posts in Sep 2018
categories: PROGRAMMING
date: 2018-10-05 00:33:28
tags: [strong type, interface, template, exception handling, virtual memory, TCP, TIME_WAIT, abstraction]
---
## Programming Language

[Strong types for strong interfaces](https://www.fluentcpp.com/2016/12/08/strong-types-for-strong-interfaces/)

How to add a type wrapper for built-in types.

Besides using phantom template parameter to avoid alias, one can also use private inheritance:

```cpp
class NamedType
{
public:
    explicit NamedType(T const& value) : value_(value) {}
    explicit NamedType(T&& value) : value_(std::move(value)) {}
    T& get() { return value_; }
    T const& get() const {return value_; }
private:
    T value_;
};

class Width : NamedType<int> {
public:
    using NamedType::NamedType;
    using NamedType::get;
};
```

---

[Passing strong types by reference – First attempt](https://www.fluentcpp.com/2016/12/12/passing-strong-types-by-reference/)

This post is the sequal of the above post.

The main problem the post tryies to solve is: how to make copy of `NamedType` values cheap.

However, I am conservative on using reference-wrapper as the solution, because doing this has to expose the lifetime of the wrapped object to public; after all, reference-wrapper is only a point per se.
<!-- more -->
I think the real solution is to make wrapped object type clear about being value semantics or reference semantics.

---

[Float vs Double](https://www.bfilipek.com/2012/05/float-vs-double.html)

Using double is usually faster than using float.

Because compilers usually promote float to double for evalution, and convert back as final result.

---

[CppCon 2015: Peter Sommerlad “Variable Templates and Compile-Time Computation with C++14"](https://www.youtube.com/watch?v=DM-RXeiSCmc)

This talk really presents some amazing magics, like generating multiplcation table on compile time...

---

[Don’t Try Too Hard! – Exception Handling](https://arne-mertz.de/2015/01/dont-try-too-hard/)

- Restrict exception types that may throw to control catch handlers.
- Use RAII instead of try/catch for cleanup in the presence of exceptions.
- Try not to change exception types.
- Be consistent in use of exceptions.
- Exceptions are part of a library’s interface but can be leaked more easily. Encapsulate them properly.
- Only catch exception if you have enough information to take actions on them.

---

[Helper Classes Deserve Some Care, Too](https://arne-mertz.de/2015/01/helper-classes-deserve-some-care-too/)

Know C++ compilation and linking model and internal linkage can be your friend.

There are other engineering advice but they have been talked too much and I decide to follow the DRY principle.

## Operating System

[Exploring Windows virtual memory management](https://www.triplefault.io/2017/08/exploring-windows-virtual-memory.html)

This post should be divided into several parts to reduce information density.

Besides, this post lacks a systematic roadmap for thinking clearly.

Maybe reading Windows Internals is a better option.

## Network

[TCP连接状态异常记录](http://blueskykong.com/2018/07/26/tcp-close-wait/)

What does too many CLOSE_WAIT connections mean and how to solve it.

---

[Coping with the TCP TIME-WAIT state on busy Linux servers](https://vincent.bernat.ch/en/blog/2014-tcp-time-wait-state-linux)

what is `TIME_WAIT` and why `net.ipv4.tcp_tw_recycle` evil

---

[HTTP2 for Dummies](https://drodriguezhdez.github.io/2018/04/30/http2-for-dummies/)

扫盲，就是内容太少了。

这样看起来 dummy 也太 dummy 了

## Architecting

[多研究些架构，少谈些框架（1） -- 论微服务架构的核心概念](http://newtech.club/2017/06/09/%E5%A4%9A%E7%A0%94%E7%A9%B6%E4%BA%9B%E6%9E%B6%E6%9E%84%EF%BC%8C%E5%B0%91%E8%B0%88%E4%BA%9B%E6%A1%86%E6%9E%B6%EF%BC%881%EF%BC%89-%E8%AE%BA%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%9E%B6%E6%9E%84%E7%9A%84%E6%A0%B8%E5%BF%83%E6%A6%82%E5%BF%B5/)

[多研究些架构，少谈些框架（2）-- 微服务和充血模型](http://newtech.club/2017/06/12/%E5%A4%9A%E7%A0%94%E7%A9%B6%E4%BA%9B%E6%9E%B6%E6%9E%84%EF%BC%8C%E5%B0%91%E8%B0%88%E4%BA%9B%E6%A1%86%E6%9E%B6%EF%BC%882%EF%BC%89-%E5%BE%AE%E6%9C%8D%E5%8A%A1%E5%92%8C%E5%85%85%E8%A1%80%E6%A8%A1%E5%9E%8B/)

[多研究些架构，少谈些框架（3）-- 微服务和事件驱动](http://newtech.club/2017/06/16/%E5%A4%9A%E7%A0%94%E7%A9%B6%E4%BA%9B%E6%9E%B6%E6%9E%84%EF%BC%8C%E5%B0%91%E8%B0%88%E4%BA%9B%E6%A1%86%E6%9E%B6%EF%BC%883%EF%BC%89-%20%E5%BE%AE%E6%9C%8D%E5%8A%A1%E5%92%8C%E4%BA%8B%E4%BB%B6%E9%A9%B1%E5%8A%A8/)

因为没有具体经验，所以上面三篇文章干货足不足不好评判

---

[Web Architecture 101](https://engineering.videoblocks.com/web-architecture-101-a3224e126947)

The basic architecture concepts I wish I knew when I was getting started as a web developer.

## MISC

[It all comes down to respecting levels of abstraction](https://www.fluentcpp.com/2016/12/15/respect-levels-of-abstraction/)

One principle to rule others all: _respecting levels of abstractions_.

Respecting levels of abstraction means that all the code in a given piece of code (a given function, an interface, an object, an implementation) must be at the same abstraction level.

Constantly ask yourself: in terms of what am I coding here.

--

[Over-generalization](https://arne-mertz.de/2015/01/over-generalization/)

Generalizations has a cost. Don’t generalize a function because you have a hunch that “some day in the future” the generalization might be useful.