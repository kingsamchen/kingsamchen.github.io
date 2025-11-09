---
title: 注意 std::views::filter
categories: PROGRAMMING
date: 2025-11-09 15:46:58
tags: [cpp, ranges, undefined behavior]
---
C++ 20 把 ranges standardize 之后看似有了一种很 fancy 很 fp-style 的实现算法的方式，但是这套东西坑也不少。

这篇 post 的主要内容来自 [CppCon 2023 | Back to Basics: Iterators in C++ - Nicolai Josuttis](https://www.youtube.com/watch?v=26aW6aBVpk0&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=6)，但是只总结 views/filter 贼坑的点。

```cpp
#include <ranges>
#include <vector>

#include "fmt/format.h"
#include "fmt/ranges.h"

int main() {
    std::vector<int> vs{1, 4, 7, 10};
    fmt::println("original = {}", vs);

    auto vs_even = vs | std::views::filter([](auto&& n) { return n % 2 == 0; });
    for (int& i : vs_even) {
        ++i;
    }

    fmt::println("incre even 1st time = {}", vs);

    for (int& i : vs_even) {
        ++i;
    }

    fmt::println("incre even 2nd time = {}", vs);

    for (int& i : vs_even) {
        ++i;
    }

    fmt::println("incre even 3rd time = {}", vs);

    for (int& i : vs_even) {
        ++i;
    }

    fmt::println("incre even 4th time = {}", vs);

    return 0;
}
```

这四个输出分别是

```
original = [1, 4, 7, 10]
incre even 1st time = [1, 5, 7, 11]
incre even 2nd time = [1, 6, 7, 11]  <-- ???
incre even 3rd time = [1, 7, 7, 11]  <-- ???
incre even 4th time = [1, 8, 7, 11]  <-- ???
```

从第二次 incre 开始就变得不符合预期

原因是 std::views::filter 为了做到 amortized constant time，实现会在内部第一次调用 begin() 的时候缓存下第一个符合 predicate 的 iterator，而且第一次调用之后这个 begin iterator 就不变了

那怎么解决修改的问题呢？

标准给的解决方案是：你可以修改 views::filter 返回的迭代器指向的对象，但是修改之后的结果也必须符合 predicate，否则就是 undefined behavior...

你就说算不算解决吧。

---

所以按照标准，下面代码（也是取自上面 cppcon）就是 UB

```cpp
for (auto& m : monsters | std::views::filter(isDead)) {
    m.resurrect();  // UB! because after modification, it is no longer dead.
}
```

StackOverflow 上也有一个类似的[讨论](https://stackoverflow.com/questions/78499483/c20-stdviews-and-caching)

---

比较蛋疼的一点是，这种 UB 目前看 ub sanitizer 扫不出来...
