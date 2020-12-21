---
title: Reverse Range Based For Loops
categories: PROGRAMMING
date: 2020-12-22 00:40:09
tags: [cpp, range-based for]
---
### 实现

```cpp
template<typename T>
class ReverseIterable {
public:
    template<typename U>
    explicit ReverseIterable(U&& u)
        : iterable_(std::forward<U>(u))
    {}

    ~ReverseIterable() = default;

    ReverseIterable(const ReverseIterable&) = delete;

    ReverseIterable& operator=(const ReverseIterable&) = delete;

    auto begin() noexcept
    {
        return std::rbegin(iterable_);
    }

    auto end() noexcept
    {
        return std::rend(iterable_);
    }

private:
    T iterable_;
};

template<typename T>
auto MakeReverseIterable(T&& iterable)
{
    // 1) if `iterable` is lvalue, then T is deduced as T&
    // 2) if `iterable` is rvalue, then T is deduced as T
    return ReverseIterable<T>(std::forward<T>(iterable));
}
```

- 如果是左值，`ReverseIterable` 里存储的是这个值的引用，是否包含 `const`-qualifier 视参数而定
- 如果是右值，`ReverseIterable` 里存值

上述类型切换刚好利用了 universal reference 的推导规则和 reference collapsing rules

手动去掉了 copy semantics，但是保留了 move semantics 因为我觉得会存在需要 move 的场景。

### 使用方法

```cpp
#include <iostream>
#include <set>
#include <vector>

template<typename T>
const T& as_const(const T& t) noexcept
{
    return t;
}

int main()
{
    std::vector v{ 1, 3, 5, 7, 9 };

    std::cout << "--- lvalue ---\n";
    for (const auto& ele : MakeReverseIterable(v)) {
        std::cout << ele << " ";
    }

    std::cout << "\n--- lvalue as const ---\n";
    for (const auto& ele : MakeReverseIterable(as_const(v))) {
        std::cout << ele << " ";
    }


    std::cout << "\n--- rvalue ---\n";

    auto gen = []() {
        return std::set{ 3, 6, 2, 9, 7 };
    };

    for (const auto& ele : MakeReverseIterable(gen())) {
        std::cout << ele << " ";
    }

    return 0;
}
```

然后就可以收工了。

一时手痒兴起之作。
