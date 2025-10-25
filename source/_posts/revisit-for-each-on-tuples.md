---
title: std::tuple 的 for-each 手法
categories: PROGRAMMING
date: 2025-10-25 17:20:08
tags: [tuple]
toc: true
---
好久没写正经技术文章了，刚好前段时间给 fawkes 加 middlewars 的遇到一些坑，刚好借这个机会温故知新/水一篇

核心思路：

- 可以编译期获得 tuple 的大小
    - `sizeof…(T)` 如果 `T…` 对应 tuple 的元素类型
    - `std::tuple_size_v<T>` 如果整个 tuple 是作为一个模板参数传入
- 有了大小之后需要构造一个 <0, 1, 2, …, N> 的 index sequence, 并且要是编译期的，目的是可以进行**解包**获得单独的 index `I`，作为 `std::get<I>()` 的参数
- `std::index_sequence<>` 刚好可以作为这样一个容器：

    ```cpp
    template<std::size_t... I>
    std::index_sequence<I...>
    ```

    并且可以用 `std::make_index_sequence<N>` 获得这样一个容器**类型**

- 另外执行函数的参数 fn 只能 forward/move 一次

## C++ 17 搭配 fold 做法

利用 fold expression 可以简化非常多

```cpp
template<typename... T, std::size_t... I, typename F>
void tuple_for_each_impl(const std::tuple<T...>& tp, std::index_sequence<I...>, F&& fn) {
    auto&& f = std::forward<F>(fn);
    (f(std::get<I>(tp)), ...);
}

template<typename... T, typename F>
void tuple_for_each(const std::tuple<T...>& tp, F&& fn) {
    using idx_seq_t = std::make_index_sequence<sizeof...(T)>;
    tuple_for_each_impl(tp, idx_seq_t{}, std::forward<F>(fn));
}

int main() {
    tuple_for_each(std::make_tuple(42, std::string{"hello"}), [](const auto& e) {
        fmt::println("{}", e);
    });
    return 0;
}
```

## C++ 20 搭配 templated lambda

利用 templated lambda，可以少一个单独的 impl 函数

```cpp
template<typename... T, typename F>
void tuple_for_each(const std::tuple<T...>& tp, F&& fn) {
    using idx_seq_t = std::make_index_sequence<sizeof...(T)>;
    []<std::size_t... I>(const std::tuple<T...>& tp, std::index_sequence<I...>, F&& fn) {
        auto&& f = std::forward<F>(fn);
        (f(std::get<I>(tp)), ...);
    }(tp, idx_seq_t{}, std::forward<F>(fn));
}
```

缺点是，gcc 上 会 warn 这个 lambda 的参数 `tp` `fn` shadow 了外部蛤属的 `tp` `fn`；Clang 和 MSVC 都没事 https://t.co/tBCXWq5mW3

讲道理这个告警是不对的…因为 scope 压根不重叠

如果真的很烦，可以改成 lambda capture

```cpp
template<typename... T, typename F>
void tuple_for_each(const std::tuple<T...>& tp, F&& fn) {
    using idx_seq_t = std::make_index_sequence<sizeof...(T)>;
    [&tp, f=std::forward<F>(fn)] <std::size_t... I>(std::index_sequence<I...>) {
        (f(std::get<I>(tp)), ...);
    }(idx_seq_t{});
}
```

看个人喜好

## Bonus: pre-C++17

最大的问题是没法用 fold expression

有两个做法：

1. 一个是 initialization_list trick；间隔太久我都忘了这个快十年前在 stackoverflow 上看到的 trick 了
2. 标准的 template recursion

https://chatgpt.com/share/68fc8d53-1490-8009-9c7e-9ffcf2024a1f
