---
title: abseil strings/str_join 源码分析
categories: PROGRAMMING
date: 2022-09-18 21:26:45
tags: [cpp, abseil, string_view]
---
Revision:

- lts_2021_03_24
- 278e0a071885a22dcd2fd1b5576cc44757299343

## 接口一览

主要提供的接口：

```cpp
// @ absl/strings/str_join.h

// tuple (heterogeneous)
template <typename... T>
std::string StrJoin(const std::tuple<T...>& value, absl::string_view separator);

// initializer_list
template <typename T>
std::string StrJoin(std::initializer_list<T> il, absl::string_view separator);

// ranged container
template <typename Range>
std::string StrJoin(const Range& range, absl::string_view separator);

// iteartor pair
template <typename Iterator>
std::string StrJoin(Iterator start, Iterator end, absl::string_view separator);
```

上面每个接口都有一个支持自定义 formatter 的重载。

上面四个接口依赖两个内部实现函数：

- `strings_internal::JoinRange` 后三个接口使用此实现
- `strings_internal::JoinAlgorithm` 第一个接口使用此实现

> 💡 absl 的 str_join 希望能支持 integral 类型的 join，以及每个输入元素是一个复合结构的 join，所以需要支持 formatter。

## Ranged Input

1. 显式指定 begin/end iterator 的输入是最一般的情况。
2. 对于 container，包括 array 和 initializer_list，会通过 `std::begin()` 和 `std::end()` 获取到 iterator range，归约到第一种情况

`strings_internal::JoinRange()` 不做实际的事情，而是统一交由 `strings_internal::JoinAlgorithm()` 处理；并且这一步是提供 formatter 的；如果没有自定义 formatter 则使用 default formatter。

```cpp
template <typename Iterator>
std::string JoinRange(Iterator first, Iterator last,
                      absl::string_view separator) {
  // No formatter was explicitly given, so a default must be chosen.
  typedef typename std::iterator_traits<Iterator>::value_type ValueType;
  typedef typename DefaultFormatter<ValueType>::Type Formatter;
  return JoinAlgorithm(first, last, separator, Formatter());
}

template <typename Range, typename Formatter>
std::string JoinRange(const Range& range, absl::string_view separator,
                      Formatter&& fmt) {
  using std::begin;
  using std::end;
  return JoinAlgorithm(begin(range), end(range), separator, fmt);
}

template <typename Range>
std::string JoinRange(const Range& range, absl::string_view separator) {
  using std::begin;
  using std::end;
  return JoinRange(begin(range), end(range), separator);
}
```

## JoinAlgorithm 的实现

### 0x0 最通用实现

对应实现函数

```cpp
template <typename Iterator, typename Formatter>
std::string JoinAlgorithm(Iterator start, Iterator end, absl::string_view s,
                          Formatter&& f) {
  std::string result;
  absl::string_view sep("");
  for (Iterator it = start; it != end; ++it) {
    result.append(sep.data(), sep.size());
    f(&result, *it);
    sep = s;
  }
  return result;
}
```

这个实现版本做的事情最简单：

1. 通过迭代器遍历每个元素
2. 利用 formatter 将元素和 `sep` 写入到结果字符串 `result` 中

另外这个算法处理连接 sep 的方式是：

1. 一开始使用空的 sep，并且每次循环先写 sep 再写 element
2. 每次迭代都会将 sep 设置为用户指定的 sep；因为 `string_view` copy 非常轻量，所以这里开销可以忽略不及

### 0x1 针对一段 std::string 或 std::string_view 的优化实现

对应函数

```cpp
template <typename Iterator,
          typename = typename std::enable_if<std::is_convertible<
              typename std::iterator_traits<Iterator>::iterator_category,
              std::forward_iterator_tag>::value>::type>
std::string JoinAlgorithm(Iterator start, Iterator end, absl::string_view s,
                          NoFormatter) {
  std::string result;
  if (start != end) {
    // Sums size
    size_t result_size = start->size();
    for (Iterator it = start; ++it != end;) {
      result_size += s.size();
      result_size += it->size();
    }

    if (result_size > 0) {
      STLStringResizeUninitialized(&result, result_size);

      // Joins strings
      char* result_buf = &*result.begin();
      memcpy(result_buf, start->data(), start->size());
      result_buf += start->size();
      for (Iterator it = start; ++it != end;) {
        memcpy(result_buf, s.data(), s.size());
        result_buf += s.size();
        memcpy(result_buf, it->data(), it->size());
        result_buf += it->size();
      }
    }
  }

  return result;
}
```

对于这两种类型，因为可以事先确定每个元素的长度，因此可以做到下面两个优化点：

1. 对最终结果字符串只有一次内存分配
2. 利用 `memcpy` 拷贝内容，依托 `memcpy` 的优化

因为确定最终字符串长度需要提前遍历一遍，因此这个优化的代价是会有两次遍历。

模板参数的 SFINAE 限制是确保范围迭代器是至少支持 forward iterator，因为需要遍历两次，不能够是 input iterator。

`std::string` 和 `std::string_view` 的选定是通过 iterator::value_type 配合 `DefaultFormatter<>` 的特化；`NoFormatter` 这里顺带充当一个 tag 作用：

```cpp
template <typename ValueType>
struct DefaultFormatter {
  typedef AlphaNumFormatterImpl Type;
};

template <>
struct DefaultFormatter<std::string> {
  typedef NoFormatter Type;
};

template <>
struct DefaultFormatter<absl::string_view> {
  typedef NoFormatter Type;
};

typedef typename std::iterator_traits<Iterator>::value_type ValueType;
typedef typename DefaultFormatter<ValueType>::Type Formatter;
```

另外，这里 sep 的连接处理使用了另外一种方式，即：

1. 先往最终结果里写入第一个元素
2. 然后再在 for-loop 里按照 sep — element 的 pair 写入

### 0x2 Join For Tuple

tuple 不是 ranged container，而且支持 heterogeneous data types，所以需要额外单独处理。

遍历 tuple 的核心是能做到编译期的 index 递增，因为 `std::get<I>` 需要一个编译期确定的 `I`。

abseil 用的方式是定义一个 function object template `JoinTupleLoop`，每个 tuple element 对应一个实例化；并且提供一个特化作为迭代终止条件。

```cpp
template <size_t I, size_t N>
struct JoinTupleLoop {
  template <typename Tup, typename Formatter>
  void operator()(std::string* out, const Tup& tup, absl::string_view sep,
                  Formatter&& fmt) {
    if (I > 0) out->append(sep.data(), sep.size());
    fmt(out, std::get<I>(tup));
    JoinTupleLoop<I + 1, N>()(out, tup, sep, fmt);
  }
};

template <size_t N>
struct JoinTupleLoop<N, N> {
  template <typename Tup, typename Formatter>
  void operator()(std::string*, const Tup&, absl::string_view, Formatter&&) {}
};

template <typename... T, typename Formatter>
std::string JoinAlgorithm(const std::tuple<T...>& tup, absl::string_view sep,
                          Formatter&& fmt) {
  std::string result;
  JoinTupleLoop<0, sizeof...(T)>()(&result, tup, sep, fmt);
  return result;
}
```
