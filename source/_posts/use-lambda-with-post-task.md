---
title: Use Lambda With PostTask
categories: PROGRAMMING
date: 2018-04-11 20:03:59
tags: [c++, chromium, base, lambda]
---
曾经为了能在 `PostTask()` 里使用 lambda，实现了一个自动将 non-capturing lambda decay 到对应函数指针，在利用 `base::Bind()` 的方案[\[1\]](https://kingsamchen.github.io/2017/01/11/automatically-convert-non-capturing-lambdas-to-normal-function-pointers/)。

但是在使用过程中发现这个方案有几个弊端：

1. 因为只能使用 non-capture lambda，因此如果需要上下文，只能通过参数传入，这样代码写起来会很蛋疼
2. VS 2013 + Resharper 的环境下，使用 `lambda_decay()` 的代码经常会出现静态分析的错误提示（不清楚是 VS2013 还是 Resharper 的问题），导致每段代码下面都会出现红色的曲线，非常惹眼

这两天因为需要重构某个模块，又顺便思考了这个问题，后来发现之前一开始思路不太对。

没有必要做一个兼容 `base::Bind()` 的解决方案，因为需要的只是在 message-loop 的 PostTask 里使用，这样只要能够将 lambda 包裹到一个 `base::Callback<R()>` 里就可以了，至于是不是利用 `base::Bind()` 完成，并不重要！

结合之前做的 `base::Callback` 到 `std::function` 的[转换](https://kingsamchen.github.io/2017/06/15/use-base-bind-with-std-function/)，很自然的想到一个最终解决方案。

代码其实非常简单：

```cpp
template<typename R>
auto InvokeLambda(const std::function<R()>& fn) -> R
{
    return fn();
}

template<typename F>
auto BindLambda(F&& lambda) -> base::Callback<typename std::result_of<F()>::type()>
{
    using return_type = typename std::result_of<F()>::type;

    return base::Bind(InvokeLambda<return_type>, std::function<return_type()>(std::forward<F>(lambda)));
}
```

判断返回类型是因为 `PostTaskAndReplyWithResult()` 会要求第一个 closure 有指定的返回值，以便第二个 clsoure 使用。

使用的时候就

```cpp
std::string s = "hello, world";
base::Closure closure = BindLambda([&s] {
    std::cout << "value of s: " << s << std::endl;
});

closure.Run();

auto cb = BindLambda([] {
    std::cout << "with int result\n";
    return 1024;
});

std::cout << cb.Run();
```

为了使用方便，我直接往 chromium base 开了一个 ext，作为以后的扩展。

因为现在 lambda 可以直接捕捉了，所以重构之后的代码看起来更具可读性；当然前提是要小心被捕捉对象的生命周期，通常这个上下文都应该使用 capture-by-value。