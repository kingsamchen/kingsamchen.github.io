---
title: 自动将 non-capturing lambda 转换为函数指针
categories: PROGRAMMING
date: 2017-01-11 00:12:14
tags: [c++, non-capturing lambda, function pointer, obs-studio]
---
在开发某科学的直播姬的过程中，经常需要在 obs-studio 处理源之后紧接着做一些事情，例如针对大图片源做自动放缩等。

obs-studio 采用一个专有的 graphics rendering thread 来渲染各种 visualizable sources，并且允许你根据需求，注册各种底层源操作事件的回调函数（obs-studio 自己称之为 signal handler）。

我在 obs signal handler 之上，利用 `std::function` 构建了一层自己的 signal-handler，将原始的 obs 事件经过合理转换后得到语义更加明确的直播姬专属事件，并且通知上层。

但是前面提到，各种源操作都是在 obs 专属的渲染线程上完成的，那么我们向 obs 注册的 handler 也必然运行在这个专属线程上；这显然不是一个好现象，因为在直播姬程序语义下，这部分操作应属于我们自己定义的 UI 线程，并且用户可见的 UI 元素的操作也必须只能在 UI 线程中完成。

所以我们需要在 signal 转换的同时切换线程，让 `std::function` 的调用发生在指定的线程。

于是这里遇到这个 post 要解决的问题：我们使用 chromium base lib 来完成各种线程操作，**但是 `base::Bind()` 不接受 lambda 作为 currying 的载体**，因为这套 lib 是在 C++ 11 标准化之前就实现的，因此 chromium team 实现这个轮子的时候并没有考虑 C++ 11 的 lambda expressions。

方案1：使用独立的函数来产生 `base::Callback`

优点是直观明确而且很传统不容易出错；

缺点是，每个函数平均只有5行不到的代码（毕竟只是通过调用 `std::function` 做一个转发），而且我们要写大量这样的函数。。。另外由于这些独立的函数都放到了源文件开头的 anonymous namespace 里，造成阅读代码时的上下文割裂。这也是当初引入 lambda 的一个很重要的原因。

方案2：使用 non-capturing lambda，但是自己手动 cast 到函数指针

优点是利用 lambda 来限制了这部分“转发”函数的可见性；

缺点是，不仅会造成上下文割裂，而且手动 cast 非常的 tedious。

思索片刻后我打算自己实现一个 `lambda_decay()`，来将 non-capturing lambda 自动转换为等价的函数指针。

借助 template 和一个 trick 可以做到如下的效果

```c++
using lpfn = void(*)(const std::string&);

lpfn Foo()
{
    return lambda_decay([](const std::string& s) { std::cout << s << std::endl; });
}

int main()
{
    auto ptr = Foo();
    ptr("hello world");
    return 0;
}
```

这样就可以直接将 lambda “嵌到” `base::Bind()` 里，极大提升幸福感。

这里说两个我并不认为是缺点的局限性：

1. 不能使用在 capturing lambdas 上。这其实是我恰好需要的，因为 capturing lambda 会有各种安全隐患，配合 `base::Bind()` 一个不小心就容易出坑；另外我的目的本来就是转换得到能被 `base::Bind()` 使用的等价函数指针，而正常情况下从 non-capturing lambdas cast 得到一个函数指针是良好定义的十分明确的行为。
2. 不能使用在 generic lambdas 上。generic lambdas 很好很有用，但是它不是银弹，尤其在配合 `base::Bind()` 使用的情况下。

接下来是实现方法

```c++
template<typename T>
struct dememberize;

template<typename C, typename R, typename... Args>
struct dememberize<R(C::*)(Args...) const> {
    using type = R(*)(Args...);
};

template<typename T>
struct lambda_pointerize_impl {
    using type = typename dememberize<decltype(&T::operator())>::type;
};

template<typename T>
using lambda_pointerize = typename lambda_pointerize_impl<T>::type;

template<typename F>
lambda_pointerize<F> lambda_decay(F lambda)
{
    return lambda_pointerize<F>(lambda);
}
```

其实如果 VS 2013 支持 `constexpr` 的话，其实用起来会更优雅。

另：我发现英文在自创单词上简直太有感觉了。。。。