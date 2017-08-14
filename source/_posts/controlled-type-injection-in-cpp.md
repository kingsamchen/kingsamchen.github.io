---
title: Controlled Type Injection in C++
categories: PROGRAMMING
date: 2017-08-15 00:15:07
tags: [c++, shared_ptr, type injection]
---
之前实现 kbase 的 `ENSURE` 宏支持如下用法

```cpp
// Throw a std::runtime_error when condition is violated.
ENSURE(RAISE, cond)(var).Require();

// Throw a MyException when condition is violated.
ENSURE(RAISE, cond)(var).Require<MyException>();
```

这几天在重新审视的时候觉得将自定义异常类型依托 `Require()` 注入是一个有问题的设计：强行耦合了 **RAISE** 这个行为的具体策略和行为无关的 `Require()` 函数，在设计语义上给人感觉莫名其妙。

更加糟糕的是，因为 `ENSURE` 宏依托的 `Guarantor` 类不是一个 class template，自定义异常类的信息没办法直接保存在类中，导致 `Require<MyException>()` 的重载实现非常丑陋...

于是我想，如果能将异常类型注入通过一个单独的函数完成，并且保存在类成员中，那上面提到的两个问题都可以得到解决

```cpp
// Call ThrowIn to configure exception type that will be raised.
// And there is only one Require().
ENSURE(RAISE, cond)(var).ThrowIn<MyException>.Require();
```

并且由于 `ThrowIn()` 函数具有 configuration 的语义，哪怕实际操作只是 performed-in-debug-only 的 `CHECK`，也不会有任何违和感

```cpp
// We configured exception type but we don't raise an exception at all.
ENSURE(CHECK, cond)(var).ThrowIn<MyException>.Require();
```

由于个人极度不希望将 `Guarantor` 变成一个 class template，因此不能使用常规方法保存这个注入的类型信息，换言之，我们大概需要一种能够将类的类型和他包含的某个成员的类型信息的关联拆开。

然后我就想到了 `std::shared_ptr`。

`std::shared_ptr` 的神奇之处在于，他的 deleter 不是自己类型的一部分，而且可以在运行期替换，which literally is what we need！

于是我就照猫画虎，写了一个 exception-pump，做类似的事情（不过我只是研究了一下实现逻辑，才没有傻到真的去看 `std::shared_ptr` 的源代码呢 XD）：

```cpp
class ExceptionPump {
public:
    virtual ~ExceptionPump() = default;
    virtual void Throw(const std::string& what) const = 0;
};

template<typename E>
class ExceptionPumpImpl : public ExceptionPump {
public:
    void Throw(const std::string& what) const override
    {
        throw E(what);
    }
};

class Guarantor {
public:
    // ...

    template<typename E>
    Guarantor& ThrowIn()
    {
        static_assert(std::is_base_of<std::exception, E>::value, "E is not a std::exception");
        exception_pump_ = std::make_unique<internal::ExceptionPumpImpl<E>>();
        return *this;
    }

    void Guarantor::DoThrow()
    {
        if (!exception_pump_) {
            exception_pump_ = std::make_unique<internal::ExceptionPumpImpl<EnsureFailure>>();
        }

        exception_pump_->Throw(exception_desc_.str());
    }

private:
    std::unique_ptr<internal::ExceptionPump> exception_pump_;
}
```

`exception_pmp_` 仅会在需要是才会创建具体对象实例，因此对仅使用 `CHECK` 行为的语句来说，这块可以认为是 zero-cost

BTW：在我改完这部分代码之后，我把 `RAISE` 改名成了 `THROW`，感觉和上下文更加 consistent 一些
