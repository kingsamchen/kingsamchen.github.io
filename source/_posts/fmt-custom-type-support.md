---
title: fmt 格式化 custom types
categories: CODE-LIFE
date: 2025-11-02 22:52:32
tags: [cpp, fmt]
---
之前用 fmt 格式化 custom types 的时候只知道特化的那个方法，并且因为需要写两个函数而且返回类型不容易记忆所以每次都是找个模板抄...

这次花了点时间完整看了一下 fmt 的 official doc，才发现其实有很多额外的方法不说，之前用特化的那个方式也不太对。

先说其他做法

对于 enum 类型，用 `U format_as(T)` 的选择是最好的，提供这个函数可以在格式化是做一个 T -> U 的类型转换，让 fmt 转而去格式化返回值

```cpp
enum class E;

auto format_as(E e) { return std::to_underlying(e); }
```

理论上也可以在这个函数里做一些复杂的操作，但是需要注意返回对象的生命周期

另外对于本甚至已经做了 `ostream operator<<` 支持的自定义类型来说，以前只知道用 `fmt::streamed(obj)`，这次翻了文档发现可以直接一劳永逸，避免每次格式化都要写一遍 `fmt::streamed()`

```cpp
struct date {
    int year, month, day;

    friend std::ostream& operator<<(std::ostream& os, const date& d) {
        return os << d.year << '-' << d.month << '-' << d.day;
    }
};

template<>
struct fmt::formatter<date> : ostream_formatter {};

std::string s = fmt::format("The date is {}", date{2012, 12, 9});
// s == "The date is 2012-12-9"
```

考虑到很多三方库都是这种情况，所以这个用法还是很值得学习的

再说 reuse existing formatter 这个方案前先提一下为啥特化的做法有点问题。

主要原因是每次做特化支持，parse 部分永远都是返回 `format_parse_context.begin()`，等于说压根不做 format specifier 支持

官方其实是推荐至少做 alignment 的支持，所以嘛...

所以这里就可以考虑复用现有的 formatter

```cpp
struct point {
    int x;
    int y;
};

template<>
struct fmt::formatter<point> : fmt::formatter<std::string_view> {
    format_context::iterator format(const point& pt, format_context& ctx) const {
        auto s = fmt::format("({}, {})", pt.x, pt.y);
        return fmt::formatter<std::string_view>::format(s, ctx);
    }
};

fmt::println("{:>10}", point{4, 2});
```

不过缺点是会引入额外的内存消耗，例如上面的 `s` 的格式化过程，最多最多可以换成 stack allocation 来避免动态分配

```cpp
template <>
struct fmt::formatter<point> : fmt::formatter<std::string_view> {
  template <class FormatContext>
  auto format(const point& pt, FormatContext& ctx) const {
    fmt::basic_memory_buffer<char, 64> buf;
    fmt::format_to(std::back_inserter(buf), "({}, {})", pt.x, pt.y);
    std::string_view sv(buf.data(), buf.size());
    return fmt::formatter<std::string_view>::format(sv, ctx);
  }
};
```

所以，zero-extra memory 和 reuse format specifier 看你怎么选了

当然你说我就是不 care format specifier 也可以
