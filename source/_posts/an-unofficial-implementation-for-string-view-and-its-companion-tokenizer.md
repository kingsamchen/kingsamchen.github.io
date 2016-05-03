---
title: 自己轮的 string_view 和它的小伙伴 tokenizer
date: 2016-05-03 22:16:03
categories: PROGRAMMING
tags: [C++ 17, string_view, tokenizer]
---

### String View

`string_view` 是 C++ 17 引入的一个基础设施，和 `array_view` 类似，表示一种 *non-owning object*。

实际上，`StringPiece` 也是一种类似 `string_view` 的实现，并且一开始也作为提供 `string_view` 的范例实现之一出现在相关的 proposal 中。

但是既然标准委员会已经钦定了 `string_view`，我们就应该用它。

不过 VS 2015 update 2 附带的 STL 实现中并没有包含 `string_view`，于是出于其他需求，我做了如下两件小事：

1. 按照[标准要求](http://en.cppreference.com/w/cpp/string/basic_string_view)，自己轮了一个 `string_view` 的[实现](https://github.com/kingsamchen/KBase/blob/master/kbase/string_view.h)。
2. 移除了 `StringPiece`，并用自己实现的 `StringView` 替换。

不过自己实现的代码，代码风格还是按照自己的偏好。



### Tokenizer

某个需求是这样的：从文本读入一段数据，数据用固定标记分隔成一段一段，需要对每段数据做相关处理。

一种直接的方式是利用 `kbase::SplitString` 将数据分段。但是 `SplitString` 会一下将所有数据分段，在数据量很大的情况下会消耗不小的空间。而如果每段数据前后没有依赖关系，那么只要每次能获取到那一段的数据即可。

`Tokenizer` 就是在这种情况下被实现出来的。

`Tokenizer` 依赖前面提到的 `StringView`，因为它实际上也是一个 non-owning viewer，不会对原始数据造成任何可修改的影响。

`Tokenizer` 利用提供的 iterator 实现对数据段的遍历，并且利用标准的 `begin` `end` 可以直接支持 C++ 11 的 ranged-base for。额外的好处是让编译器对和 `end()` 比较的代码进行优化。

虽然从标准而言，`begin()` 和 `end()` 应该具有常数时间复杂度，但是在以 token 为目标数据的条件下，`begin()` 很难做到常数时间复杂度，一般情况下需要 $O(l)$ 的复杂度，其中 $l$ 为数据段长度。不过好在初始调用并不频繁。

一个简单的范例看起来大概是这样的

```c++
std::string str = "anything that cannot kill you makes you stronger.\n\tsaid by Bruce Wayne\n";
std::vector<std::string> exp { "anything", "that", "cannot", "kill", "you", "makes", "you",
                              "stronger", "said", "by", "Bruce", "Wayne" };
Tokenizer tokenizer(str, " .\n\t");
size_t i = 0;
for (auto&& token : tokenizer) {
  if (!token.empty()) {
    EXPECT_EQ(exp[i], token.ToString());
    ++i;
  }
}
```

具体实现见[这里](https://github.com/kingsamchen/KBase/blob/master/kbase/tokenizer.h)。