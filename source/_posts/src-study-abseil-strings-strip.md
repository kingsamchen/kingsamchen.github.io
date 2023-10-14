---
title: abseil strings/strip 源码分析
categories: PROGRAMMING
date: 2023-10-15 00:09:59
tags: [cpp, abseil]
---
Revision:
- lts_2022_06_23
- 273292d1cfc0a94a65082ee350509af1d113344d
- `absl/strings/strip.h`
- `absl/strings/ascii.h`

strip 系列函数有点类似其他语言/库中常见的 trim 系列函数，但是又不完全一致。

## StripPrefix/StripSuffix

```cpp
// StripPrefix()
//
// Returns a view into the input string `str` with the given `prefix` removed,
// but leaving the original string intact. If the prefix does not match at the
// start of the string, returns the original string instead.
ABSL_MUST_USE_RESULT inline absl::string_view StripPrefix(
    absl::string_view str, absl::string_view prefix) {
  if (absl::StartsWith(str, prefix)) str.remove_prefix(prefix.size());
  return str;
}

// StripSuffix()
//
// Returns a view into the input string `str` with the given `suffix` removed,
// but leaving the original string intact. If the suffix does not match at the
// end of the string, returns the original string instead.
ABSL_MUST_USE_RESULT inline absl::string_view StripSuffix(
    absl::string_view str, absl::string_view suffix) {
  if (absl::EndsWith(str, suffix)) str.remove_suffix(suffix.size());
  return str;
}
```

strip 要求完全匹配，所以配合 `StartsWith()/EndsWith()` 和 `string_view` 的 `remove_prefix()/remove_suffix()` 让实现变得非常的 trivial。

## ConsumePrefix/ConsumeSuffix

```cpp
inline bool ConsumePrefix(absl::string_view* str, absl::string_view expected) {
  if (!absl::StartsWith(*str, expected)) return false;
  str->remove_prefix(expected.size());
  return true;
}

inline bool ConsumeSuffix(absl::string_view* str, absl::string_view expected) {
  if (!absl::EndsWith(*str, expected)) return false;
  str->remove_suffix(expected.size());
  return true;
}
```

这两个函数是修改入参版本的 strip，并且唯一的好处是可以直接通过返回值来判断是否进行了 strip。

实际上我个人觉得这两个函数有点多余：

- string_view 本身是一个非常轻巧的类型，直接通过返回值再复制并没有什么开销
- 而可以通过直接比较返回的 sv 的 size 来判断是否发生了 strip

不过说起来我过去的经历中并没有遇到需要感知是否 strip 的场景

## Strip (ASCII) Whitespaces

对于需要把一个字符串左边/右边或者两边的空白符去掉的函数在 `ascii.h` 中，因为移除的是 ascii whitespaces…

```cpp
// Returns absl::string_view with whitespace stripped from the beginning of the
// given string_view.
ABSL_MUST_USE_RESULT inline absl::string_view StripLeadingAsciiWhitespace(
    absl::string_view str) {
  auto it = std::find_if_not(str.begin(), str.end(), absl::ascii_isspace);
  return str.substr(static_cast<size_t>(it - str.begin()));
}

// Strips in place whitespace from the beginning of the given string.
inline void StripLeadingAsciiWhitespace(std::string* str) {
  auto it = std::find_if_not(str->begin(), str->end(), absl::ascii_isspace);
  str->erase(str->begin(), it);
}

// Returns absl::string_view with whitespace stripped from the end of the given
// string_view.
ABSL_MUST_USE_RESULT inline absl::string_view StripTrailingAsciiWhitespace(
    absl::string_view str) {
  auto it = std::find_if_not(str.rbegin(), str.rend(), absl::ascii_isspace);
  return str.substr(0, static_cast<size_t>(str.rend() - it));
}

// Strips in place whitespace from the end of the given string
inline void StripTrailingAsciiWhitespace(std::string* str) {
  auto it = std::find_if_not(str->rbegin(), str->rend(), absl::ascii_isspace);
  str->erase(static_cast<size_t>(str->rend() - it));
}

// Returns absl::string_view with whitespace stripped from both ends of the
// given string_view.
ABSL_MUST_USE_RESULT inline absl::string_view StripAsciiWhitespace(
    absl::string_view str) {
  return StripTrailingAsciiWhitespace(StripLeadingAsciiWhitespace(str));
}

// Strips in place whitespace from both ends of the given string
inline void StripAsciiWhitespace(std::string* str) {
  StripTrailingAsciiWhitespace(str);
  StripLeadingAsciiWhitespace(str);
}
```

```cpp
// Array of bitfields holding character information. Each bit value corresponds
// to a particular character feature. For readability, and because the value
// of these bits is tightly coupled to this implementation, the individual bits
// are not named. Note that bitfields for all characters above ASCII 127 are
// zero-initialized.
ABSL_DLL const unsigned char kPropertyBits[256] = {
    0x40, 0x40, 0x40, 0x40, 0x40, 0x40, 0x40, 0x40,  // 0x00
    0x40, 0x68, 0x48, 0x48, 0x48, 0x48, 0x40, 0x40,
    0x40, 0x40, 0x40, 0x40, 0x40, 0x40, 0x40, 0x40,  // 0x10
    0x40, 0x40, 0x40, 0x40, 0x40, 0x40, 0x40, 0x40,
    0x28, 0x10, 0x10, 0x10, 0x10, 0x10, 0x10, 0x10,  // 0x20
    0x10, 0x10, 0x10, 0x10, 0x10, 0x10, 0x10, 0x10,
    0x84, 0x84, 0x84, 0x84, 0x84, 0x84, 0x84, 0x84,  // 0x30
    0x84, 0x84, 0x10, 0x10, 0x10, 0x10, 0x10, 0x10,
    0x10, 0x85, 0x85, 0x85, 0x85, 0x85, 0x85, 0x05,  // 0x40
    0x05, 0x05, 0x05, 0x05, 0x05, 0x05, 0x05, 0x05,
    0x05, 0x05, 0x05, 0x05, 0x05, 0x05, 0x05, 0x05,  // 0x50
    0x05, 0x05, 0x05, 0x10, 0x10, 0x10, 0x10, 0x10,
    0x10, 0x85, 0x85, 0x85, 0x85, 0x85, 0x85, 0x05,  // 0x60
    0x05, 0x05, 0x05, 0x05, 0x05, 0x05, 0x05, 0x05,
    0x05, 0x05, 0x05, 0x05, 0x05, 0x05, 0x05, 0x05,  // 0x70
    0x05, 0x05, 0x05, 0x10, 0x10, 0x10, 0x10, 0x40,
};

// ascii_isspace()
//
// Determines whether the given character is a whitespace character (space,
// tab, vertical tab, formfeed, linefeed, or carriage return).
inline bool ascii_isspace(unsigned char c) {
  return (ascii_internal::kPropertyBits[c] & 0x08) != 0;
}
```

这部分代码都很简单，可以发现：

1. 每款函数有两个重载，返回 `string_view` 的非修改版和接受 `std::string*` 的原地修改版
Google C++ Style Guide 要求修改参数的类型要用 `T*` 而不是 `T&` 在这里占了重载的便宜…
2. 不管 strip 哪个方向，用的都是 `std::find_if_not()` 这个算法函数，区别在于迭代器类型和 `substr()/erase()` 的处理
3. ascii whitespace 利用的是函数 `absl::ascii_isspace()` 这个函数的是显示查表。absl 给 0 ~ 255 每个 ascii 值进行了属性bit编码，例如这里的属于 whitespace 一类在 bit（1 << 3）会被设置。
4. 注意 `StripAsciiWhitespace()` 是先 strip trailing 的再 strip leading 的，这里是一个有意的优化
