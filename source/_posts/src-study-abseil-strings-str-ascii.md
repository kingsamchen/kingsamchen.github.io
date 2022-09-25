---
title: abseil strings/ascii 源码分析
categories: PROGRAMMING
date: 2022-09-25 23:10:37
tags: [cpp, abseil]
---
Revision:

- lts_2021_03_24
- 278e0a071885a22dcd2fd1b5576cc44757299343
- `absl/strings/ascii.h`

## ASCII To Lower & To Upper

对于单个 ascii 字符的 tolower 和 toupper，直接采用了查表法

```cpp
// ascii_tolower()
//
// Returns an ASCII character, converting to lowercase if uppercase is
// passed. Note that character values > 127 are simply returned.
inline char ascii_tolower(unsigned char c) {
  return ascii_internal::kToLower[c];
}

// ascii_toupper()
//
// Returns the ASCII character, converting to upper-case if lower-case is
// passed. Note that characters values > 127 are simply returned.
inline char ascii_toupper(unsigned char c) {
  return ascii_internal::kToUpper[c];
}
```

- 查表下标需要 $\ge 0$，所以参数上会要求 `unsigned char` 将范围控制在 $[0, 255]$
- 某些架构的CPU对于 cmov 等类似指令的 scale 不佳；而某些架构的CPU对于分支预测做的不太好；所以综合下来直接查表不会有性能缺点
- 限定 ASCII 之后 lower/upper case 的转换就不需要考虑 locale 了

---

有了上面两个基础，针对字符串的 str-to-lower/upper 就非常直接了

```cpp
void AsciiStrToLower(std::string* s) {
  for (auto& ch : *s) {
    ch = absl::ascii_tolower(ch);
  }
}

void AsciiStrToUpper(std::string* s) {
  for (auto& ch : *s) {
    ch = absl::ascii_toupper(ch);
  }
}
```
