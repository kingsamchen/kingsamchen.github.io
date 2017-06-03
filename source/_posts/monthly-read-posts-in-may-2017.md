---
title: Monthly Read Posts in May 2017
categories: PROGRAMMING
date: 2017-06-03 14:51:40
tags: [c++, 断点下载, exception, shared_ptr, lambda, mixin]
---
[http resumable download](http://cizixs.com/2015/10/06/http-resume-download)

虽然之前写直播姬自动更新时实现过续传下载，但是功能规范上并没有太完备；而这篇文章很好的补充了几个断点续传中，严格实现会遇到的几个 key points。

例如：不应当假设资源一定支持续传，要首先使用 request header `range` 检查目标资源是否支持续传，支持 `range` 的 http resonse code 是 206。

另外，两次断点下载期间，资源可能发生变化，需要在请求时同时附带上 `Etag` 或 `Last-modified` 记录，由服务器确定资源是否变化。

[Why Exceptions Should be Exceptional](http://mattwarren.org/2016/12/20/Why-Exceptions-should-be-Exceptional/)

文章从性能角度阐述了为什么不能将 exception 机制作为 routine control flow。

但是个人认为这个切入角度不好，因为很容易给人一种异常机制开销大的错觉，导致读者之后避开使用异常。

异常处理一直都是一个大麻烦，相比 error code handling 不够直观，没有足够的经验很难控制好，加上某些语言自身特性导致固有复杂度暴涨（例如 C++）。

[shared-ptr](http://shaharmike.com/cpp/shared-ptr/)

文章大部分的内容（除了 aliasing）其实 *Effective Modern C++* 里都有...

[Magical Captureless Lambdas](https://adishavit.github.io/2016/magical-captureless-lambdas/)

核心总结出来就是一句话：Captureless lambdas 能够自动转换为对应的 C-Style 函数指针，而在 MSVC 里，implicit cast 能够自动处理不同的 calling convention

[Leaky Closures Captureless Lambdas](https://adishavit.github.io/2016/leaky-closures-captureless-lambdas/)

An entity that is mentioned or used but is not ODR-used within the lambda body, does not need to be captured in the capture list.

Informally, an object is odr-used if its address is taken, or a reference is bound to it.

[Lambdas Callbacks](http://videocortex.io/2016/lambdas-callbacks/)

又名：如何用 capturing lambdas 作为 c-style callbacks

[Combining Static and Dynamic Polymorphism with C++ Template Mixins](https://michael-afanasiev.github.io/2016/08/03/Combining-Static-and-Dynamic-Polymorphism-with-C++-Template-Mixins.html)
[C++ Mixins - Reuse through inheritance is good when done the right way](http://www.thinkbottomup.com.au/site/blog/C%20%20_Mixins_-_Reuse_through_inheritance_is_good)

Mixins pattern in C++ 快速导读

[The Very Real Mess of Virtual Functions](https://mortoray.com/2016/08/10/the-very-real-mess-of-virtual-functions/)

十足标题党

并且个人认为，作者试图解决一个前提错误的问题。

文中以 `Init()` 和 `UnInit()` 为例阐述观点，然而，这正好说明了为什么如无必要不应该使用 two-phase initialization

文中试图实现的机制不就是 ctor 和 dtor 所做的么……

而对于其他可能需要子类调用父类虚函数的场景，我认为，non-vitual-interface idiom已经足够了。

BTW：就工程领域而言，可能对 GUI 框架来说，文中的一些套路还是有用的；不过因为个人在这方面没有什么经验，因此持保留意见