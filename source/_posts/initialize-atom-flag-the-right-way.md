---
title: 正确地初始化 std::atomic_flag
categories: PROGRAMMING
date: 2018-01-23 15:01:46
tags: [c++, atomic_flag, atomic operations]
---
根据标准文档的要求，`std::atomic_flag` 只有利用 `ATOMIC_FLAG_INIT` 初始化之后，才获得一个确定的初始状态。

现在假设我们要自己实现一个 spin-lock，那么只需要利用 `std::atomic_flag` 实现一个 spin-mutex：

```cpp
class SpinMutex {
public:
    SpinMutex()
        : flag_(ATOMIC_FLAG_INIT)
    {}

    void lock() {
        while (flag_.test_and_set(std::memory_order_acquire)) {}
    }
 
    void unlock() {
        flag_.clear(std::memory_order_release);
    }
 
private:
    std::atomic_flag flag_;
};
```

事实上，知名著作 *C++ Concurrency in Action 1st Edition* 中介绍 `std::atomic_flag` 时用的也是一模一样的代码。

然而，这段代码在比较新的 C++ 编译器上会编译失败。

VS2017 直接提示失败，Clang 3.9 出现一个警告。

问题的根源是，标准委员会对：如何时用 `ATOMIC_FLAG_INIT` 初始化 `std::atomic_flag` 存在分歧和反复，导致 C++ 11 和 C++ 14 在这个点上存在不一致。

万能的 StackOverflow 刚好有一个[相关的问题](https://stackoverflow.com/questions/24437396/stdatomic-flag-as-member-variable)

### 正确的做法

正确的做法仍然是遵循标准要求的 *used in the copy-initialized context*，即：

```cpp
class SpinMutex {
public:
    void lock() {
        while (flag_.test_and_set(std::memory_order_acquire)) {}
    }
 
    void unlock() {
        flag_.clear(std::memory_order_release);
    }
 
private:
    // Initialize to clear state.
    // Don't use initialization list for `flag_`.
    std::atomic_flag flag_ = ATOMIC_FLAG_INIT;
};
```

### MISC

在 VS2017 中，可以在 member initialization list 中用 `{}` 而不是 `()` 初始化，能正常编译通过。但是同一做法在其他编译器上没有效果。

因此，在这种涉及 unspecified 的点上，尽量使用标准的做法，避免之后更换编译器踩坑。
