---
title: 避免在类设计上混合 Owner 及 View 语义
categories: PROGRAMMING
date: 2016-07-10 11:08:00
tags: [c++, owner semantics, view semantics]
---
这个论断来自于前段时间重构 [pickle](https://github.com/kingsamchen/KBase/blob/master/kbase/pickle.h) 时的想法。

## The Big Picture

`Pickle` 是一个二进制序列化设施，内部维护一块 buffer 用以保存被序列化的二进制数据，语义上看，一个 `Pickle` 对象对内部 buffer 具有无争议的 resource owner 语义。

反序列化设施 `PickleReader` 被设计为从一个给定的 `Pickle` 对象构造，并且在反序列化过程中只改变 `PickleReader` 内部的 reader cursor，不会对 `Pickle` 内部的 buffer 有任何 side-effect。显然，`PickleReader` 对 `Pickle` 的内部 buffer 具有 view 语义。

为了支持能从一块给定的 raw buffer of serialized data 进行反序列化（这是很常见的需求，只要序列化后的数据涉及持久化操作，例如存储在磁盘上），`Pickle` 被设计为允许从给定的 read-only buffer 构造，以方便继续构造 `PickleReader` 对象进行反序列化操作；并且出于性能和逻辑考虑，`Pickle` 只维护指向这块 read-only buffer 的指针，并不拷贝一份 buffer 数据。

为了区分这种情况，在 read-only buffer 上构造的 `Pickle` 对象的 buffer capacity 始终为 `size_t(-1)`。

```c++
class Pickle {
private:
    struct Header {
        uint32_t payload_size;
    };
public:
    Pickle();

    // Creates a Pickle object that has weak-reference to a serialized buffer.
    // The Pickle object cannot call any modifiable methods, and caller must ensure
    // the referee is in a valid state, when PickleReader is applied.
    Pickle(const char* data, int data_len);

    // Makes a deep copy of the Pickle object.
    Pickle(const Pickle& other);

    Pickle(Pickle&& other);

    // Makes a deep copy of the Pickle object.
    Pickle& operator=(const Pickle& rhs);

    Pickle& operator=(Pickle&& rhs);

    ~Pickle();

    ...

    // Returns true, if this object is weakly bound to a serialized buffer.
    bool readonly() const
    {
        return capacity_ == kCapacityReadOnly;
    }

private:
    static constexpr const size_t kCapacityReadOnly = static_cast<size_t>(-1);
    Header* header_;
    size_t capacity_;
    ...
};
```

不经意间，`Pickle` 被我加上了 view 语义。

## What's the Problem ?

问题始于对 assignment operators，尤其是 move-assignment 的修改。

一开始我发现我遗漏了对 evil self-assignments 的处理，加上一个非常简陋的 patch 之后我开始发觉事情没有这么简单。

assignments 的麻烦之处在于它涉及到两个方面的资源处理：原始资源和新资源。如果是 move assignment，那么还要考虑到被移动对象的资源的 ownership。

那么接下来考虑这样一个问题： **创建一个 owned pickle 对象，以及在这个 pickle 对象的 buffer 上创建一个 view pickle 对象，然后将 view pickle 对象赋给（copy and move） owned pickle 对象**。

注意，这种行为是合乎逻辑合乎语义的。因为 `Pickle` 具有值语义，无论是 owner 还是 view，都允许 duplication 行为。

接下来想一想，上面的操作会出现什么样的可能后果？以及，为了做到正确的行为，需要做什么样的 workaround？

你会发现你掉进了一个大坑，为了避免出现错误，你需要不断的修改你的 assignment 语义以适应可能出现的神奇情况。

如果赋值操作需要同时保证复制对象具有的 owner/view 语义，呃，这似乎是不可能的...

如果赋值操作只需要保证创建的 `PickleReader` 等效，那么可以在进行赋值前检查两个 `Pickle` 是否 ref 同一块数据内存，如果是，则直接返回。

但是注意，这个规定在语义上是一个很大的 degeneration。

当然还有其他 workarouond，但是实质上都不能摆脱它们只是不恰当语义下的丑陋的遮羞布。

而这一切的根本原因在于一个对象可能表现出 owner 语义，也可能表现出 view 语义；这实质上是 evil self-assignment 的一个延伸，即不同 resource 语义下 ref 同一个 resource。

想一想实现一个 copy-on-write 的 string 类可能要付出的代价，哪怕只考虑单线程。

一个 COW-string 能够从一个 view 语义对象悄悄的 transform 到一个 owner 语义对象；而这不起眼的 transformation 不意外的挖了一个大坑，没有经验的人可能直接跌得尸骨无存。

有兴趣的可以看看 Herb Sutter 在 *More Exceptional C++* 中解决这个坑所引入的语义技巧。

对了，`std::shared_ptr` 和 `std::weak_ptr` 倒是很机智的用两个对象来表示 owner-view 语义。

## Conclusion

这篇 post 的目的不是说完全不能这样设计，而是说在可能的前提下尽量拆分语义，毕竟挖了大坑把别人埋进去了不是一件值得炫耀的事情。

最后吐槽一下 chromium 组的某些工程师，虽然我承认 chromium 确实是一个成功的 C++ Project，但是他们有些人真的是不理解 C++，也只是把 C++ 当作一个加强版的 Java 来用。