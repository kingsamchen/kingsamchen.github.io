---
title: abseil strings/string_view 源码分析
categories: PROGRAMMING
date: 2022-09-12 23:51:47
tags: [cpp, abseil, string_view]
---
Revision:

- LTS_20210324.2
- 278e0a071885a22dcd2fd1b5576cc44757299343

这个模块是 absl 提供的和 C++ 17 保持兼容的 string_view 实现。

## NUL-Terminated String Length Computation

主要用于从一个 null-terminated c-style string 构造 string_view 的重载

```cpp
constexpr string_view(const char* str);
```

因为要考虑 `constexpr`-ness，所以大多时候都不会使用 trivial `strlen()`。

```cpp
static constexpr size_type StrlenInternal(const char* str) {
#if defined(_MSC_VER) && _MSC_VER >= 1910 && !defined(__clang__)
    // MSVC 2017+ can evaluate this at compile-time.
    const char* begin = str;
    while (*str != '\0') ++str;
    return str - begin;
#elif ABSL_HAVE_BUILTIN(__builtin_strlen) || \
    (defined(__GNUC__) && !defined(__clang__))
    // GCC has __builtin_strlen according to
    // https://gcc.gnu.org/onlinedocs/gcc-4.7.0/gcc/Other-Builtins.html, but
    // ABSL_HAVE_BUILTIN doesn't detect that, so we use the extra checks above.
    // __builtin_strlen is constexpr.
    return __builtin_strlen(str);
#else
    return str ? strlen(str) : 0;
#endif
  }
```

MSVC 上手动实现一个 `constexpr length()`

GCC & Clang 上直接使用 `__builtin_strlen()`

> 💡 C++ 17 开始 `char_traits::length()` 是 `constexpr`，所以如果只考虑 C++ 17 之后的场景，应该可以直接用这个。
见：https://en.cppreference.com/w/cpp/string/char_traits/length

### Special handling for nullptr

这个重载的注释写明了如果 `str` 可能是 `nullptr`，则使用 `absl::NullSafeStringView(str)`。

```cpp
// NullSafeStringView()
//
// Creates an `absl::string_view` from a pointer `p` even if it's null-valued.
// This function should be used where an `absl::string_view` can be created from
// a possibly-null pointer.
constexpr string_view NullSafeStringView(const char* p) {
  return p ? string_view(p) : string_view();
}
```

猜测是获取 c-style 字符串长度的函数都已经默认参数是 non-null，例如 `strlen()`。

C++ 23 会引入一个专门被标记为 deleted 的重载来避免可能存在的意外：

```cpp
constexpr basic_string_view(std::nullptr_t) = delete;  // since C++ 23
```

## std::string → string_view

abseil 的做法是 string_view 提供一个接受 `std::basic_string<>` 的重载

```cpp
template <typename Allocator>
  string_view(  // NOLINT(runtime/explicit)
      const std::basic_string<char, std::char_traits<char>, Allocator>&
          str) noexcept
      // This is implemented in terms of `string_view(p, n)` so `str.size()`
      // doesn't need to be reevaluated after `ptr_` is set.
      : string_view(str.data(), str.size()) {}
```

这个算是比较符合直觉的做法。

> 💡 这个 ctor 是 implicit 的

### How about STL

C++ 标准库的做法就比较“另类”了：它选择为 std::string 提供一个 implicit `operator std::basic_string_view()`。

## string_view → std::string

abseil 的做法是提供一个 `explicit std::basic_string<> operator`：

```cpp
  // Explicit conversion operators

  // Converts to `std::basic_string`.
  template <typename A>
  explicit operator std::basic_string<char, traits_type, A>() const {
    if (!data()) return {};
    return std::basic_string<char, traits_type, A>(data(), size());
  }
```

### How about STL

这部分同样是实现在 `std::basic_string` 中：

```cpp
template < class T >
explicit constexpr basic_string( const T& t,
                                 const Allocator& alloc = Allocator() );

template < class T >
constexpr basic_string( const T& t, size_type pos, size_type n,
                        const Allocator& alloc = Allocator() );
```

此处会针对 `T` 做 string_view 做 type-trait assert

---

不管是哪种实现，从 string_view → string 都是**显式完成**的，因为需要重新分配内存拷贝内容。

## find_*_of 的查找优化

abseil::string_view 在实现这部分函数时，对于要检查的字符序列超过一个字符时，会在内部使用 lookup-table 来加速，使得这部分函数的复杂度从标准的 $O(size()*v.size())$ 提升到 $O(size())$。

对于长字符序列有一定的优化效果

```cpp
class LookupTable {
 public:
  // For each character in wanted, sets the index corresponding
  // to the ASCII code of that character. This is used by
  // the find_.*_of methods below to tell whether or not a character is in
  // the lookup table in constant time.
  explicit LookupTable(string_view wanted) {
    for (char c : wanted) {
      table_[Index(c)] = true;
    }
  }
  bool operator[](char c) const { return table_[Index(c)]; }

 private:
  static unsigned char Index(char c) { return static_cast<unsigned char>(c); }
  bool table_[UCHAR_MAX + 1] = {};
};

string_view::size_type string_view::find_first_of(string_view s,
                                                  size_type pos) const
    noexcept {
  if (empty() || s.empty()) {
    return npos;
  }
  // Avoid the cost of LookupTable() for a single-character search.
  if (s.length_ == 1) return find_first_of(s.ptr_[0], pos);
  LookupTable tbl(s);
  for (size_type i = pos; i < length_; ++i) {
    if (tbl[ptr_[i]]) {
      return i;
    }
  }
  return npos;
}
```
