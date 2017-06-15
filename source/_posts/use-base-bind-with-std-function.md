---
title: Use base::Bind With std::function
categories: PROGRAMMING
date: 2017-06-15 22:10:23
tags: [c++, chromium, base::Bind, std::function, function object]
---
`base::Bind()` 和 `base::Callback` 可以看作是对标准库 `std::bind()` 和 `std::function` 的模拟；因为 chromium 项目早在 C++ 11 正式通过前就已经存在好多年了。

我为直播姬设计新的网络通信基础组件时，接口的回调 handler 通常设计为 `std::function` 对象，因为它可以“吸收”任何函数对象，大多数情况下这个设计运转的非常良好；但是这里有一个略微棘手的问题： `base::Callback` 对象无法被 `std::function` 使用。

问题本质很简单，因为 `base::Callback` 不是一个 function object，因为它不支持以函数形式调用（没有提供 `operator()` 的重载），instead，它提供了一个 `Run()` 函数来执行这个 callback...

虽然我之前吐槽过好多次 chromium 的很多架构设计完全是 Java-Style 的，但是这里并不打算再拎出来批判一次，我们要聚焦的是如何解决这个问题。

解决思路很简单：既然它不是一个 function object，那么我们就给他包一层 function object 的皮就好了：

```cpp
#include "base/bind.h"

template<typename>
class CallableCallback;

template<typename R, typename... Args>
class CallableCallback<R(Args...)> {
public:
    using callback_t = base::Callback<R(Args...)>;

    explicit CallableCallback(callback_t callback)
        : callback_(callback)
    {}

    R operator()(Args... args) const
    {
        return callback_.Run(args...);
    }

private:
    callback_t callback_;
};

template<typename Callback>
auto MakeCallable(Callback callback)->CallableCallback<typename Callback::RunType>
{
    using Sig = typename Callback::RunType;
    return CallableCallback<Sig>(callback);
}
```

辅助函数 `MakeCallable()` 可以将一个 `base::Callback` 对象转换成一个 function object。

一个简单的示例如下：

```c++
int Inc(int i)
{
    return ++i;
}

void Foo(const std::string&, std::string, int)
{}

class Test {
public:
    Test()
        : msg_("test"), weak_ptr_factory_(this)
    {}

    void Bark(const std::string& data)
    {
        std::cout << data << ":\t" << msg_ << std::endl;
    }

    std::string msg_;
    base::WeakPtrFactory<Test> weak_ptr_factory_;
};

int main()
{
    std::cout << "--- test case 1 ---\n";
    std::function<int(int)> fn(MakeCallable(base::Bind(&Inc)));
    std::cout << fn(5) << std::endl;

    std::cout << "--- test case 2 ---\n";
    Test* ptr = new Test();
    std::function<void()> mfn(
        MakeCallable(base::Bind(&Test::Bark, ptr->weak_ptr_factory_.GetWeakPtr(), "inside")));
    mfn();
    std::cout << "release test instance\n";
    delete ptr;
    mfn();

    return 0;
}
```

小巧优雅纯天然