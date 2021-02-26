---
title: UUID 及其实现 1
categories: PROGRAMMING
date: 2021-02-25 00:44:30
tags: [uuid, RFC 4122]
toc: true
---
## 数据格式

UUID 是 _Universally Unique IDentifier_ 的缩写，本质上是一个 128-bit (16-byte) 长的标识符，可以用32个16进制数表示，并且通常以如下形式展示：

```text
// 每组字符长度分别是 8-4-4-4-12
XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
              ^    ^
              M    N
```

上面 `M` 和 `N` 出的数字分别表示了这个 UUID 使用的 version 和 variant；具体下面展开。

HEX 字符存在大小写，按照 RFC 4122 要求，UUID 输出时 `'a'-'f'` 应为小写；但是在作为输入时大小写不敏感。

因为 UUID 的生成不需要中心管理节点 (central authority)，是天然分布式；也正因如此，没有一个验证 UUID 是否有效的方法，最多只能验证是否满足数据格式要求。

### 0x0 Variant

上面 `N` 处的 HEX 的 3-bit MSB 指明了 UUID 使用的 variant。

Variant 决定了当前 UUID 其他的 bits 如何 interpret。这里的 bits 也包含 `M` 处指示 version 的那部分。

语义上讲，variant 更适合被叫做 type
<!-- more -->
注：`x` 代表 don't-care value

```
   Msb0  Msb1  Msb2  Description
  --------------------------------
    0     x     x    Reserved, NCS backward compatibility.
    1     0     x    The variant specified in this document.
    1     1     0    Reserved, Microsoft Corporation backward compatibility
    1     1     1    Reserved for future definition.
```

RFC 4122 里使用的是第二行的 variant，这也是最常用的一个版本。

考虑到具体可能的值，这个版本的 variant 在 N 处有以下取值：

```
1 0 0 0 = 8
1 0 0 1 = 9
1 0 1 0 = A
1 0 1 1 = B
```

这意味着你能见到的几乎所有的 UUID 在 N 处的值是 8, 9, A, B 中的一个。

### 0x1 Version

M 处的整个 4-bit 的 MSB 指明 UUID 用的 version。

在前面确定了 RFC 使用的 variant 前提下，目前 RFC 支持的版本如下：

```
   Msb0  Msb1  Msb2  Msb3   Version  Description

    0     0     0     1        1     The time-based version specified in this document.

    0     0     1     0        2     DCE Security version, with embedded POSIX UIDs.

    0     0     1     1        3     The name-based version specified in this document
                                     that uses MD5 hashing.

    0     1     0     0        4     The randomly or pseudo-randomly generated version
                                     specified in this document.

    0     1     0     1        5     The name-based version specified in this document
                                     that uses SHA-1 hashing.
```

version 1 是基于时间戳和设备 ID；version 2 是 1 的衍生私货版本。

version 3 & 5 均属于外部给定一些内容，利用这些内容生成目标 UUID；3 & 5的区别仅仅在于使用的 hash 算法不一样。

version 4 是完全 PRNG 的版本，也是使用的最多的。

注：version 其实更适合用 sub-type 这个名字（其实完全可以这么理解）

### 0x2 字节序

如果使用整型来表示一个 UUID，例如 2 个 64-bit 或者 4 个 32-bit，就需要注意数据的字节序。

UUID 存储上使用的是 big-endian，即网络字节序。

### 0x3 The Nil UUID

Nil UUID 的所有 bit 都是 0。

也就是 `00000000-0000-0000-0000-000000000000`

## 实现：UUID v4

因为 UUID v4 是使用最多的版本，而且也是最容易实现的版本，所以我们先研究如何实现 UUID v4。

注：用于生产环境的 v4 的高性能实现还是会有一些坑的，这个后面会讲到。

算法步骤：

1. 首先生成 128-bit 的随机数，具体怎么表示任选；可能会有性能差异
2. 设置 variant 部分，即 N 处所在的部分的最高两位为 1 和 0
3. 设置 version 部分，即 M 处最高 4 位为 0 1 0 0
4. 然后就可以收工了

UUID v4 的生成只用到随机数，不依赖任何设备信息和额外存储。

PS：具体代码实现会在讲解完所有版本的实现之后一起给出；因为单独实现一个版本和支持所有版本的实现在设计上有不同的考虑。
