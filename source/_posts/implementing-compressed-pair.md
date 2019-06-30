---
title: 实现 compressed pair
categories: PROGRAMMING
date: 2019-06-30 17:58:25
tags: [c++, ebo, unique_ptr, optimization]
---
C++ 的 `std::unique_ptr` 有个神奇的特性：如果使用默认的 deleter（即使用 `operator delete`），或者 non-capturing lambda 作为 deleter，则有

```c++
sizeof(std::unique<T>) == sizeof(void*);
```

即整个对象的内存布局和 trivial pointer 一致，没有额外的开销。

这个特性的背后就是 compress-pair；这个设施能够在某个元素是一个 empty class 时避免为其分配内存。

注：这里假设你知道什么是 EBO，以及为什么会有 EBO。

这里自己动手实现一个 compressed pair：

```c++
template<typename Tx, typename Ty, bool = std::is_empty<Tx>::value>
struct CompressedPair : Tx {
    Ty second;

    template<typename T>
    explicit CompressedPair(T&& t)
        : Tx(),
          second(std::forward<T>(t))
    {}

    template<typename T, typename U>
    CompressedPair(T&& t, U&& u)
        : Tx(std::forward<T>(t)),
          second(std::forward<U>(u))
    {}

    Tx& get_first() noexcept
    {
        return *this;
    }

    const Tx& get_first() const noexcept
    {
        return *this;
    }
};

template<typename Tx, typename Ty>
struct CompressedPair<Tx, Ty, false> {
    Tx first;
    Ty second;

    template<typename T, typename U>
    CompressedPair(T&& t, U&& u)
        : first(std::forward<T>(t)),
          second(std::forward<U>(u))
    {}

    Tx& get_first() noexcept
    {
        return first;
    }

    const Tx& get_first() const noexcept
    {
        return first;
    }
};
```

因为 EBO 是实现的核心，而父类的构造顺序先于子类的任何成员，上面将 `Tx` 作为可被优化的成员。
<!-- more -->
上面的代码使用 partial specialization 来分离处理 1）可优化 2）不可优化 两种 case：

- 可优化的，直接使用 EBO；同时因为有继承，所以要访问 `first` 只需要做 up-cast 即可
- 不可优化的，两个部分直接按照常规的 pair 实现

PS：`is_empty` 这里是必需的，并且现代编译器下的实现是直接利用 compiler intrinsic；单纯的 library implementation 会有很大制约。可以参考 https://stackoverflow.com/questions/35531309/how-is-stdis-emptyt-implemented-in-vs2015-or-any-compiler

PPS：上面忽略 `Tx` 是 final class 的 case，因为这不是我们这个 demo 的重点。

有了 `CompressedPair` 做基础，我们只要糊上一个前端就可以。

假设我们要实现一个 `OutputChannel`，可以将内部保存的元素花式输出：

```c++
template<typename T, typename P = default_printer<T>>
class OutputChannel {
public:
    template<typename U>
    explicit OutputChannel(U&& data)
        : cp_(std::forward<U>(data))
    {}

    template<typename U, typename V>
    OutputChannel(U&& data, V&& p)
        : cp_(std::forward<V>(p), std::forward<U>(data))
    {}

    void Log()
    {
        cp_.get_first()(cp_.second);
    }

private:
    CompressedPair<P, T> cp_;
};

template<typename T>
struct default_printer {
    void operator()(const T& ele)
    {
        std::cout << ele << std::endl;
    }
};
```

如果我们使用默认的 printer 或者 non-capture lambda，则不会给我们的 instance 增添内存开销：

```c++
OutputChannel<std::string> lc("hello world");
std::cout << "sizeof(lc) = " << sizeof(lc) << std::endl;
static_assert(sizeof(std::string) == sizeof(lc), "error");
lc.Log();

OutputChannel<std::string, void(*)(const std::string&)> lsc("hello world", &SPrint);
std::cout << "sizeof(lsc) = " << sizeof(lsc) << std::endl;
static_assert(sizeof(std::string) == sizeof(lsc) - sizeof(void*), "error");
lsc.Log();

auto up_printer = [](const std::string& s) {
    std::string us;
    std::transform(s.begin(), s.end(), std::back_inserter(us),
                    [](char c) { return static_cast<char>(toupper(c)); });
    std::cout << us << std::endl;
};

OutputChannel<std::string, decltype(up_printer)> usc("hello world", up_printer);
std::cout << "sizeof(usc) = " << sizeof(usc) << std::endl;
static_assert(sizeof(std::string) == sizeof(usc), "error");
usc.Log();
```

代码返利参考：https://github.com/kingsamchen/Eureka/tree/master/CompressedPair/compressed_pair
