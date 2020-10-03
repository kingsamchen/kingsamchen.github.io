---
title: 禁用某些构造函数
categories: PROGRAMMING
date: 2020-10-02 15:28:10
tags: [cpp, sfinae]
---
有时候我们希望用更明确的自定义类型取代一些 primitives，依靠类型系统来减少一些人为错误：

```cpp
void Run(WithMultithreading mt, WithAdvancedMode advanced);

// Both of them work like bool
auto mt = WithMultithreading(true);
auto mode = WithAdvancedMode(false);

Run(mt, mode);
```

类型 `WithMultithraeding` 和 `WithAdvancedMode` 用起来很像 `bool`，但是他们是两个不同的类型，混用会出现编译错误。

```cpp
Run(mode, mt);  // compile failure; type mis-match
```

不管我们使用 `typedef` 还是 `using`，都只能定义 `bool` 的类型别名；在类型系统看来他们还是一回事。
<!-- more -->
一个比较常用的方案是使用 class template + type tag，即

```cpp
template<typename T>
class TaggedBool {
    bool value_;

public:
    // would that be enough?
    explicit constexpr TaggedBool(bool value)
        : value_(value)
    {}

    constexpr explicit operator bool() const noexcept
    {
        return value_;
    }

    ...
};

using WithMultithreading = TaggedBool<struct MultithreadingTag>;
using WithAdvancedMode = TaggedBool<struct AdvancedModeTag>;
```

注：tag type 不需要提前定义，直接在需要定义 type alias 提供一个即可。

然而上面的构造函数定义**无法保证** `TaggedBool` 只能从 `bool` 或者其他 `TaggedBool` 初始化。

因为如下的代码仍然能编译通过，虽然在某些严格编译模式下会有告警。

```cpp
int i = 123;
WithMultithreading mt(i);

// 如果认为可以使用 {} 初始化防止 narrowing conversion
// 那么考虑下面这个 case

// still implicit conversion, but not narrowing.
WithMultithreading mt2 {nullptr};
```

问题的核心是某个类型可以隐式转换到 `bool`，进而再调用 `TaggedBool` 的构造函数。

`explicit` 和 `{}` 初始化在这里都没有什么帮助。

Rant：如果问我最不喜欢哪个 C++ 特性，implicit conversion 绝对名列前茅。

如果我们能提供接受这些参数的构造函数版本，并将它们标记为 `delete`，那么编译器便会选中这些被”删除“的函数，触发编译错误；以做到禁止这些构造函数的目的。

同时因为我们需要禁用一系列的构造函数，我们最好通过函数模板来减少工作量。

我们需要对函数模板进行类型限制，目前最常见的做法就是利用 SFINAE，未来 C++ 20 的 concepts 普及后可以直接利用 concepts。

```cpp
template<typename T>
class TaggedBool {
    bool value_;

public:
    // Allow implicit conversion from bool
    explicit constexpr TaggedBool(bool value)
        : value_(value)
    {}

    template<typename U,
             typename=std::enable_if_t<
                 (std::is_integral_v<U> && !std::is_same_v<U, bool>) ||
                 std::is_floating_point_v<U> ||
                 (std::is_pointer_v<U> || std::is_null_pointer_v<U>)
             >>
    constexpr TaggedBool(U) = delete;

    ...
```

这样一来，如果我们使用其他integral类型，浮点类型，甚至指针类型都会强迫编译器选择这个被 delete 的函数版本，强制触发编译失败。

完整代码可以见[这里](https://github.com/kingsamchen/Eureka/blob/master/cxx-alias-types/alias/tagged_bool.h)
