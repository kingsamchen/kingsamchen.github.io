---
title: 复习：DCLP 和 Memory Reordering
categories: PROGRAMMING
date: 2019-10-01 12:29:03
tags: [cpp, singleton, memory-reordering]
---
本文是对 Jeff Preshing 的 [Double Checked Locking Is Fixed in C++ 11](https://preshing.com/20130930/double-checked-locking-is-fixed-in-cpp11/) 笔记。

### 0x00 传统实现

因为 synchronization 只需要在实例第一次创建时保证；此后（ `instance != nullptr` 时）都不需要锁来保证 synchronization。

在第一次判断实例为空和上锁之间存在一个 potential race，因此上锁后需要再一次判断实例是否为空。

这也是 double checked 的来由。

所以一个传统但并不100%正确的 DCLP 实现如下：

```c++
class Singleton {
public:
    static Singleton* GetInstance()
    {
        if (instance == nullptr) {
            std::lock_guard<std::mutex> lock(mtx);
            if (instance == nullptr) {
                instance = new Singleton();				// <-- key point
            }
        }

        return instance;
    }

private:
    // omit explicit static field initialization.
    static std::mutex mtx;
    static Singleton* instance;
};
```

这基本是早起 C++ DCLP 的实现架子。

### 0x01 问题：内存乱序以及为什么锁帮不了忙

语句

```c++
instance = new Singleton();	
```

在 C++ 中实际上等效于

```c++
tmp = operator new(sizeof(Singleton));  // step 1: allocate memory via operator new
new(tmp) Singleton;                     // step 2: placement-new for construction
instance = tmp;                         // step 3: assign addr to instance
```

注意：其中除了分配内存是固定第一步之外，构造对象和赋值内存地址的**生成代码顺序**是由编译器自己决定。这里的顺序只是一种可能性。
<!-- more -->
多线程环境下，DCLP 要能正常工作的一个前提是：如果某个线程 看到 `instance` 非空，那么 `instance` 对应的对象必须是构造完毕的；==i.e. 执行 step 3 之前，step 1 和 step 2 必须是已经完成的。==

不幸的是，因为代码优化的需求，导致两个后果：

1. 编译器生成代码时会存在乱序
2. 现代多核CPU不光每个核存在执行指令乱序，同一段代码不同核同一时刻的执行的顺序都可能是不同的

这就会导致上面的例子中，可能出现：

1. 线程 A 先更新了 `instance` 地址到新内存
2. 线程 B 进入函数，检查发现 `instance` 不为空；但是此时对象尚未构造完毕，使用对象就跪了

另外，这里即使你自己引入一个临时对象 `tmp`，也解决不了问题。因为生成的代码本来就用了临时对象，这个解决不了乱序的问题。

**无助的🔒**

一个不是很容易被理解的点是，这里明明用了锁来保证 synchronization，为什么也会出问题呢？

尤其对一些比较“资深“的开发者来说，他们知道锁本身是带有 memory fence 功能的。

这里为了方便说明问题，我们按照语句拆解后的版本来分析：

```c++
static Singleton* GetInstance()
{
    if (instance == nullptr) {
        lock.Lock();                                // <-- acquire lock
        if (instance == nullptr) {
            tmp = operator new(sizeof(Singleton));  // step 1: allocate memory via operator new
            instance = tmp;                         // step 2: assign addr to instance
            new(instance) Singleton;                // step 3: placement-new for construction
        }
        lock.Unlock();                              // <-- release lock
    }

    return instance;
}
```

首先，锁本身确实是可以作为 guard variable 来保障 acquire-release 语义：Lock 操作充当 read-acquire operation，Unock 操作充当 write-release operation。

其次，acquire-release 语义提供了一个可见性保证：a write-release operation synchronizes-with a read-acquire operation.

这个约束下，一个线程在临界区内的任何写操作在 unlock 之后，对其他任意**进入**临界区的线程都是可见的。

但是很不幸的事，DCLP 实现下，第一次判断，即L3，是在临界区外。

所以

- 临界区内的代码虽然不能重排出临界区，但是相互之间仍旧可以重排

- 某个线程在 L3 发现 `instance` 不为空不能保证这个对象在另外一个线程已经构造完毕。

### 0x02 C++ 11 和内存模型

在 C++ 11 之前，C++ 标准里的 abstract machine 是单线程的，也自然的没有 memory model 相关的规定。

因此：

- 编译器的乱序优化只保证单线程内的 observable behavior 不变
- 没有标准内的东西能够指导编译器或者 CPU 来抵抗乱序

随着 modern C++ 的时代到来，这些问题都被稳妥地解决了。

现在利用 C++ atomic 设施可以正确实现 DCLP：

```c++
class Singleton {
public:
    static Singleton* GetInstance()
    {
        auto tmp = instance.load(std::memory_order_acquire);    // <-- read-acquire operation
        if (tmp == nullptr) {
            std::lock_guard<std::mutex> lock(mtx);
            tmp = instance.load(std::memory_order_relaxed);     // <-- relaxed is enough
            if (tmp == nullptr) {
                tmp = new Singleton();
                instance.store(tmp, std::memory_order_release); // <-- write-release operation
            }
        }

        return tmp;
    }

private:
    static std::mutex mtx;
    static std::atomic<Singleton*> instance;
};

```

L5 和 L11 通过 acquire-releaes 语义保证了正确的内存顺序。

L8 不需要额外约束，原因我们前面已经说了。

**使用 acquire / release fence**

上面的例子是通过 acquire-release operations 来避免乱序，如果你看过我之前的[某篇文章](https://kingsamchen.github.io/2019/08/31/acquire-release-operations-and-fences/)，我们知道使用 memory fence 可以产生更强的内存序约束。

```c++
static Singleton* GetInstance()
{
    auto tmp = instance.load(std::memory_order_relaxed);            // <-- relaxed
    std::atomic_thread_fence(std::memory_order_acquire);            // <-- acquire fence
    if (tmp == nullptr) {
        std::lock_guard<std::mutex> lock(mtx);
        tmp = instance.load(std::memory_order_relaxed);
        if (tmp == nullptr) {
            tmp = new Singleton();
            std::atomic_thread_fence(std::memory_order_release);    // <-- release fence
            instance.store(tmp, std::memory_order_relaxed);         // <-- relaxed
        }
    }

    return tmp;
}
```

### 0x03 平台架构

在 X86/64 架构上，读/写操作自带 acquire-release 语义，因此上面的代码的作用基本是指导编译器生成代码。不对 CPU 的执行有影响。

生成的代码会直接是用一个简单的 `mov` 指令。

https://gcc.godbolt.org/z/wkrfCZ

不过如果在上面的例子里，使用 seuqntial consistent 语义，即 atomic 操作不带 memory-order 参数，则即使在 X86/64 平台上，也会生成带有 full barrier 的指令，例如 `xchg`。

参见：https://gcc.godbolt.org/z/0zpCqJ
