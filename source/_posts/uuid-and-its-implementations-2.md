---
title: UUID 及其实现 2
categories: PROGRAMMING
date: 2021-03-06 11:33:30
tags: [uuid]
toc: true
mathjax: true
---
上篇介绍了 UUID 的格式和 v4 的实现，这篇讲 v1 & v2 的实现。

v2 版本是 v1 上衍生出来的，而 v1 因为涉及到时间戳，又称为 time-based implementation。

## 实现 UUID v1

v1 除了常规的 variant 和 version 部份外，还涉及三个部分：
- timestamp，需要 60-bit 通常用 64-bit 容纳；非寻常的 Unix Timestamp
- clock sequence，需要 14-bit，通常用 16-bit 存储
- node identifier，48-bit 和设备相关

下面逐一解释这三个部分。

### 0x0. UUID Timestamp

和常见的 Unix 时间戳不同，UUID 时间戳记录的是，自 1582-10-15 00:00:00 至今的 100-ns 数。

1582年10月15日的零点是[格里高力历](https://zh.wikipedia.org/zh-cn/%E6%A0%BC%E9%87%8C%E6%9B%86)开始的时间，这个时间也是 UUID Epoch Time。

UUID 时间戳的分辨率（resolution）是 100 纳秒，即每 100ns 为一个 tick。

现代编程语言都可以获取 unix 时间戳，因此可以先获取 unix 时间戳，然后加上两个 epoch time 的差值；这个差值是个定值，等于 `122192928000000000`。

注意：获取时间戳时需要注意系统或者语言库能提供的分辨率。

### 0x1. Clock Sequence

因为引入了时间戳，所以存在可能由于时钟回拨、闰秒，甚至因为系统提供的时钟分辨率不够导致使用了重复的时间戳。

另外 node identifier 也有可能被改变。

所以为了尽可能保证 UUID 的唯一性，引入了 clock sequence。

注：RFC 4122 没有规定 UUID v1 必须要使用 steady clock，所以使用能够被回拨的 system clock 也是允许的。另外，即使使用了 steady clock，机器在两次启动之间也可能调整了硬件时间，一样可能会导致回拨。

Clock sequence 一开始会被初始化为一个随机值；之后如果某次获取 UUID 时间戳发现 <= 上一次的时间戳，就会自增 clock sequence。

clock sequence 通常会设置为一个无符号数，以确保溢出 wrap-around 的行为是确定的。
<!-- more-->
同时为了防止因为系统的时钟分辨率过低，导致 clock sequence 消耗过快，可以在 clock sequence **连续自增** 一定次数后 spin 一会儿，直到时间戳变化。

### 0x2. Node Identifier

这部分本质是设备信息，通常使用设备的 MAC 地址，刚好 48-bit。

如果设备有超过一块网卡，那么可以使用任意一块。

如果设备没有网卡，或者获取 MAC 地址失败，可以使用随机生成的 48-bit 数来代替，同时需要设置第一个（自左向右）8位的最低为 0x01 来表示这种情况。

### 0x3. 持久化？服务化？

根据 RFC 4122 的内容，UUID v1 的生成一开始的定位更像是系统全局服务，多个进程都可以通过这个服务申请 v1 版本的 UUID。

服务化也会带来状态：时间戳、clock sequence 和 node identifier 都可以持久化到本地存储，待到下次系统启动后从存储读入。

但是我个人非常不赞成这种模式。

服务化显著增加了对资源的的竞争，并且引入状态之后还涉及了外部存储的维护，更蛋疼的是还没有什么收益。

好在 RFC 里还留了一手可以避免这个情况的出现：首次运行或者外部存储数据发生损坏等导致不可用的情况下，执行全新初始化逻辑。

其中 clock sequence 是随机生成的，可以一定程度上避免重复 UUID 的出现。

所以我们只需要把状态限制在库使用者上，并且使用者的进程生命周期作为一次状态周期，这样既不需要持久化状态，也不需要对外共享服务。

### 0x4. 实现步骤

1. 获取 UUID 时间戳和 clock sequence
2. 获取 node identifier（鉴于生命周期内 node id 不会发生变化，因此可以只获取一次然后复用）
3. 按照展示格式写入 uuid data
    ```
    ts_low_32_bit | ts_medium_16_bit | ts_high_16_bit | cs_high_8_bit + cs_low_8_bit | node_id_48_bit
    ```
4. 设置 variant
5. 设置 version

这里不管底层是采用两个 8-byte 保存还是 16-byte 的数组，只需要保证最后格式化后字符串的顺序符合上面这个顺序即可。

### 0x5. Pros & Cons of v1

v1 的优点能想到的大概只有一台设备上生成的 UUID 值按照时间递增。

缺点倒是挺多的；第一个就是实现起来比较麻烦，尤其是获取 MAC 地址。

第二个是，如果要做到 RFC fully compliant，那成本会很高。

第三个，脑拍感觉出现重复的概率可能会高于全随机的 v4。

## 实现 UUID v2

v2 号称是 DCE Security Version，但是 RFC 对这部分着墨不多（其实就是基本没有）

根据 opengroup 上 DCE 当年的[文档](https://pubs.opengroup.org/onlinepubs/9696989899/chap5.htm#tagcjh_08_02_01_01)，v2 在 v1 的基础上做了如下改动。

引入了一个 _local domain_ 的概念，local domain 对于当前设备所处的 local host 是有意义的。

虽然定义上这个 _local domain_ 的取值可以是 $ [0, 2^{8}-1] $ 但是实际上只有 person, group, org 三个 domain 再用；即 $ [0, 2] $。

local domain 对应的值叫 local identifier：

- 对于 local domain == person，local identifier 就是 POSIX 系统下的 user-id，即 POSIX UID
- 对于 local domain == group，local identifier 是 POSIX GID

注意：对于 non-POSIX 系统，UUID v2 是没有定义的；比如 Windows...🤦‍

有了上面两个玩意儿之后，对 v1 的数据做如下覆写：

1. `cs_low_8_bit` 用 local domain 替代
2. `ts_low_32_bit` 用 local id 替代
3. 更新 version 字段

### 0x0 一些吐槽

v2 几乎完全是 DCE 自己定义的，并且大堆概念定义的上下文都限定在 DCE 系统内部。

甚至对于 v2 UUID uniqueness 的保证文档是这么说的：

> Concerning the uniqueness of security-version UUIDs, see the discussion in Privilege (Authorisation) Service (PS) of the double-UUID identification scheme. The point of that discussion is that the security architecture of DCE depends upon the uniqueness of security-version UUIDs only within the context of a cell; that is, only within the context of the local RS's (persistent) datastore, and that degree of uniqueness can be guaranteed by the RS itself (namely, the RS maintains state in its datastore, in the sense that it can always check that every UUID it maintains is different from all other UUIDs it maintains). In other words, while security-version UUIDs are (like all UUIDs) specified to be "globally unique in space and time", security is not compromised if they are merely "locally unique per cell". This statement does not relax the requirements on implementations of security-version UUIDs, it is only a comment about the trust structure of DCE, namely, entities (security subjects and objects) need not trust that foreign RSs maintain globally unique UUIDs, only that their own local RS maintains locally unique UUIDs.

DCE 对外公开的文档甚至只提供了 v1 的代码示例：

> Note that the manner in which security-version UUIDs are generated is implementation-dependent: no API routine is supported by DCE that generates security-version UUIDs (recall that uuid_create() specified in the referenced Open Group DCE 1.1 RPC Specification generates only version 1 UUIDs).

我甚至怀疑他们根本没有真正使用过 UUID v2，只是提出了这个标准规范；所以到底什么是真正符合要求的 v2 实现可能谁也说不准。

所以，在今天的生产环境中，不建议使用 UUID v2。
