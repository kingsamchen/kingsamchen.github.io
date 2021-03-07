---
title: UUID 及其实现 3
categories: PROGRAMMING
date: 2021-03-07 18:17:42
tags: [uuid]
toc: true
---
这一篇来讲 v3 和 v5 的实现。

这两个实现要求使用方提供：

- 一个称为 namespace 的 UUID
- 一个叫 name 的数据块（不限于字符串）

然后利用一个 hash 算法算得结果，作为目标 UUID 的基础数据（还需要额外设置 variant 和 version）。

即，对于 v3/v5 的实现，本质上是

```
uuid = hash(namespace, name)
set_variant(uuid)
set_version(uuid, ver)
```

v3 和 v5 区别在于，前者是用 MD5，后者使用 SHA-1。

并且可以发现，对于相同的 namespace 和 name，生成的结果 UUID 是一样的。

这个就是 v3/v5 版本的特性。

借用 RFC 的描述就是：

- The UUIDs generated at different times from the same name in the same namespace MUST be equal.
- The UUIDs generated from two different names in the same namespace should be different (with very high probability).
- The UUIDs generated from the same name in two different namespaces should be different with (very high probability).
- If two UUIDs that were generated from names are equal, then they were generated from the same name in the same namespace (with very high probability).

所以 v3/v5 的版本又被叫做 name-based implementation
<!-- more-->
### 预定义 namespace UUID

RFC 预定义了一些 namespace UUID

```c
/* Name string is a fully-qualified domain name */
uuid_t NameSpace_DNS = { /* 6ba7b810-9dad-11d1-80b4-00c04fd430c8 */
    0x6ba7b810,
    0x9dad,
    0x11d1,
    0x80, 0xb4, 0x00, 0xc0, 0x4f, 0xd4, 0x30, 0xc8
};

/* Name string is a URL */
uuid_t NameSpace_URL = { /* 6ba7b811-9dad-11d1-80b4-00c04fd430c8 */
    0x6ba7b811,
    0x9dad,
    0x11d1,
    0x80, 0xb4, 0x00, 0xc0, 0x4f, 0xd4, 0x30, 0xc8
};

/* Name string is an ISO OID */
uuid_t NameSpace_OID = { /* 6ba7b812-9dad-11d1-80b4-00c04fd430c8 */
    0x6ba7b812,
    0x9dad,
    0x11d1,
    0x80, 0xb4, 0x00, 0xc0, 0x4f, 0xd4, 0x30, 0xc8
};

/* Name string is an X.500 DN (in DER or a text output format) */
uuid_t NameSpace_X500 = { /* 6ba7b814-9dad-11d1-80b4-00c04fd430c8 */
    0x6ba7b814,
    0x9dad,
    0x11d1,
    0x80, 0xb4, 0x00, 0xc0, 0x4f, 0xd4, 0x30, 0xc8
};
```

### Hash 时注意字节序

不管内部实现如何存储 UUID 的数据，计算 Hash 时 namespace UUID 的数据的写入一定要按照等价于 `char[16]` 的方式。

同时，name 数据块的写入必须使用 network byte order（i.e. big-endian）。

否则会在 Hash 计算这一步就和标准出现不一致。

---

至此，UUID 的格式分析和5种实现到这里都讲完了。

后面的文章会开始从0到1的用 C++ 实现一个 uuid library。

只所以采用 C++ 有两个原因：

1. 个人需要经常复习/练习一下
2. C++ 虽然有抽象，但是没有厚重的 runtime，因此实现上不仅可以直击灵魂，而且更容易调优
