---
title: atoi With Overflow Handled
categories: PROGRAMMING
date: 2020-05-04 15:03:26
tags: [atoi, golang]
---
leetcode 上有一题是让你实现一个 `atoi()`，但是因为函数类型是 32-bit int，所以可以直接内部转换到 64-bit int，避免需要直接处理溢出的情况。

如果要针对 `int64` 实现一个 `atoi()` 又该如何做？

我翻了一下 golang 的标准库源码，大致梳理了一下

(1) 如果对象是 `int32`，并且输入字符串有效数据长度小于10位，则一定不会溢出，所以可以直接处理

(2) 否则统一走 `int64` 处理；`int64` 内部会先处理掉符号位 `neg`，然后转成 `uint64`

(3) 溢出处理的核心部分：

```golang
// Cutoff is the smallest number such that cutoff*base > maxUint64.
// Use compile-time constants for common cases.
var cutoff uint64
switch base {
case 10:
	cutoff = maxUint64/10 + 1
case 16:
	cutoff = maxUint64/16 + 1
default:
	cutoff = maxUint64/uint64(base) + 1
}

if n >= cutoff {
	// n*base overflows
	return maxVal, rangeError(fnParseUint, s0)
}

maxVal := uint64(1)<<uint(bitSize) - 1

n *= uint64(base)

n1 := n + uint64(d)
if n1 < n || n1 > maxVal {
	// n+v overflows
	return maxVal, rangeError(fnParseUint, s0)
}
n = n1
```

(4) `cutoff` 是防止溢出的第一重栅栏

对于 `uint64` 来说

```
cutoff     = 1844674407370955162
UINT64_MAX = 18446744073709551615
```

parse 的时候如果当前数已经 >= cutoff 那么后面的数字不管是几都绝对溢出。

但是即使 cutoff 这里满足，加上后一位数字之后仍然可能会溢出，所以这里要做溢出处理。

因为类型是 uint64，所以只可能发生上溢出，数值会 wrap-around，所以用 `n + uint64(d) < n` 来判断

`n1 > maxVal` 是用来处理 int32 的时候的情况（函数的输入类型已经被转成了 64-bit）

(5) 如果输入类型本身就是无符号，那么到这里就结束了。

如果是有符号，需要判断最终结果是否落在范围里，另外要注意补码体系下两端的值是不对称的。
