---
title: Wicked Data Type Promotion
categories: PROGRAMMING
date: 2016-07-05 21:19:08
tags: [data type promotion, c++, c]
---
昨天在给 [KAdBlockEngine](https://github.com/kingsamchen/KAdBlockEngine) 的 `AdFilter` 加上序列化/反序列化的支持时，意外的又一次被 data type promotion 坑了，导致一晚上的时间都在 debug...

## Yesterday-Case Once More

因为做 deserialization 时需要数据校验，所以在序列化到磁盘前会先对数据做一遍 MD5-checksum，然后再将 checksum 和实际数据分别写入磁盘。

类似的，反序列化时需要从读入的文件数据里分出 checksum 和实际数据，对实际数据再做一遍 MD5-checksum 后和上次保存的 checksum 进行比对。

```c++
kbase::Path snapshot_file(L"...");
std::string file_data = kbase::ReadFileToString(snapshot_file);
if (!file_data.empty()) {
    kbase::MD5Digest checksum;
    auto snapshot_data = file_data.data() + sizeof(checksum);
    auto snapshot_data_size = file_data.size() - sizeof(checksum);
    kbase::MD5Sum(snapshot_data, snapshot_data_size, &checksum);
    if (std::equal(checksum.cbegin(), checksum.cend(), file_data.cbegin())) {
        kbase::PickleReader snapshot(snapshot_data, snapshot_data_size);
        AdFilter ad_filter = AdFilter::FromSnapshot(snapshot);
        ad_filters_.push_back(AdFilterPair(filter_file, std::move(ad_filter)));
        return;
    }
}
```

于是神奇的事情出现了：当我用 `std::equal` 去比较两个 checksum 数据时，结果始终是 false，即使我在 debugger 的 memory watch 中确认了两块数据是一样的。

更神奇的是，顺手把 `std::equal` 换成 `memcmp` 之后，整段代码都能够正常工作。

看来问题出在 `std::equal`；But how come ?

## Thinking and Resolving

一开始怀疑是 `std::equal` 的实现问题，于是自己用 `for(...)` 手工展开，结果却惊人地和 `std::equal` 一致，并且根据调试的反馈，应该是第一个字节比较就不等返回了。

这说明 `std::equal` 的实现是正确的，问题出在使用方式上。

于是下一秒瞥到了一个惊人的发现：`MD5Digest` 的数据类型是 `std::array<uint8_t>`，而 file_data 却是 `std::basic_string<char>`；而 `char` 在 MSVC 上是 signed data type。

直觉告诉我我掉到了 **_data type promotion_** 这个天坑里。

在 C/C++ 里对(unsigned) char/short 进行算术运算时，出于性能考虑，编译器会首先将数据扩展到 int(unsigned int)，再进行实际的运算。这个过程通常称之为 **_promotion_**。

但是问题来了，signed/unsigned 数据类型在扩展时使用的指令是不一样的。

考虑如下示例代码：

```c++
unsigned char lhs = -98;
char rhs = -98;
std::cout << (lhs == rhs);
```

首先，比较的结果肯定是 `false`，不然上下文就没意义了不是...

接下来看一下对应的汇编代码：

```assem
    unsigned char lhs = -98;
00007FF60EA717DE  mov         byte ptr [lhs],9Eh  
    char rhs = -98;
00007FF60EA717E2  mov         byte ptr [rhs],9Eh  
    std::cout << (lhs == rhs);
00007FF60EA717E6  movzx       eax,byte ptr [lhs]  
00007FF60EA717EA  movsx       ecx,byte ptr [rhs]  
00007FF60EA717EE  cmp         eax,ecx  
```

明显，对无符号类型的扩展使用的指令是 `movzx`，扩展时用0填充；对有符号类型的扩展指令是 `movsx`，扩展时使用**符号位**。

这是 two's complement 结构体系下的必然选择。

但是这里有个明显的坑，如果原始数据在有符号下是负数，亦即上例中的情况，那么扩展中会用符号位填充，扩展之后高位都变成了FF..；而无符号扩展则会使用0填充，扩展之后高位都是00..；这就导致扩展后的两个数据不想等。

回到一开始的坑。

因为 `std::equal` 默认会使用 `std::equal_to<>` 作为比较的 predicate，而 `std::equal_to<>` 等价于使用 `==` 进行比较；而比较的两方数据又分别是 `char` 和  `uint8_t`，于是出现 promotion，导致错误。

而 `memcmp` 直接比较两块内存的 bits，不会发生 promotion。

## Afterthoughts

其实 CSAPP 在某一章有专门提到 data type promotion 这个问题，不过时隔多日，真是转头就忘...

这个天坑说明一个道理，涉及无符合和有符号的整型比较时需要引起注意；而如果整型是 char/short 类型则更要注意。

最好的解决方案是避免写除混合类型比较且没有使用 cast 明确标识的代码。

这次为了偷懒直接用 std::string 保存内存块数据，最后也算是报应把... -''-