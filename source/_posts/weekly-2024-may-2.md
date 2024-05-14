---
title: 一周杂记 in Week 2 May 2024
categories: CODE-LIFE
date: 2024-05-13 23:33:27
tags: [杂记]
---
本周（05/06 ~ 05/12）是5月份的第二周。

## Life

\#1

这周跑了三次富阳，分别是：周二下午接媳妇儿回家；周四晚上又送媳妇儿去富阳，然后因为媳妇儿周五只上半天班，所以周五中午又把媳妇儿接回来了。

但是周六早上实在起不来了，所以周六上午媳妇儿自己打车去了。

这周天气比较高，富阳那边明显感觉到非常热，所以这次可以完全确定车的空调制冷有问题了...

约了下周一的离家比较近的一家极氪家检查，到时候先看看是啥问题

这提车一个月就出了这种问题，我真的是有点无语了...

\#2

本周观影

- **哥斯拉-1.0** 2/5 实在太一般了...男女主的日式演技太尬了...难怪安藤樱这篇还能拿个最佳女配…另外这反战思考只能算作文艺型擦边，哥斯拉都不舍得上特效，有些镜头道具感太重了
- **海洋深处 In the Heart of the Sea** 3.5/5 看完才发现是朗霍华德导演的...后半段起伏比预想得差点，但是整体还是稳当的，就是这 cast 确实有点厉害
- **可怜的东西 Poor Things** 3/5 不知道怎么评价这电影，但是石头姐的表演确实到位，从什么都不懂到找到了自我的转折表现得很好；画面构图也是有独特的风格。整体怎么说呢，总给我一种很邪门的感觉

\#3

这周六五一调休上班，没有休假，一周只有周日单休，感觉还是挺累的...

感觉我现在这个年纪已经不太能扛住单休或者996的工作强度了

## Work

\#1

本周学习

**CppCon 2021 | Generic Graph Libraries in C++20 - Andrew Lumsdaine & Phil Ratzloff**

- 其实就是 Boost Graph Library https://www.boost.org/doc/libs/1_85_0/libs/graph/doc/index.html
- 演讲者是BGL的三个实现者之一，他们希望能把这个库标准化

**CppCon 2021 | C++20 ❤ SQL - John Bandela**

- 作者在 cppcon 2021 上还做过一个 meta-struct 的 TMP library 的分享，这个 talk 是基于 meta-struct 做的一些 compile-time SQL ORM/utils
- 用了很多 tmp tricks 感觉可以学习一下
- 不过我依然觉得某些操作其实可以考虑 boost.pfr?

**CppCon 2021 | Back to Basics: Casting - Brian Ruth**

- 比较细致的讲了 C++ 的四大 cast 及其使用场景，还提了一嘴相似的 move/forward/bit_cast
- 干货比较多

**CppCon 2021 | Deducing this Patterns - Ben Deane**

- 详细介绍了 deducing this 这个特性，演讲者是 proposal 的三个合作者之一
- deducing this 的本质是引入了 explicit this parameter，并且这个参数可以指定类型，在这个基础之上配合类型推导（可以是模板参数，也可以利用函数参数的 auto），即可推导出 this 对应的类型，这样就省掉了 const/non-const，lvalue/rvalue 的逐一实现
- 利用 deducing this 的副作用，有些场合可以不再需要 CRTP

    ```cpp
    struct base {
        void print_me(const char* src) const {
            std::cout << "base::print_me from " << src << "\n";
        }

        auto func1(this const base& self) {
            self.print_me("func1");
        }

        auto func2(this auto&& self) {
            self.print_me("func2");
        }
    };

    struct derived : base {
        void print_me(const char* src) const {
            std::cout << "derived::print_me from " << src << "\n";
        }
    };

    derived d{};
    d.func2();  // derived::print_me from func2
    ```

- 同时终于可以实现 recursive lambda 了…

    ```cpp
        auto fib = [](this auto self, int n) {
            if (n < 2)
                return n;
            return self(n - 1) + self(n - 2);
        };
        std::cout << fib(6);
    ```

- 另外，现在标准里，this 的类型是可以 pass-by-value 的

**C++ Weekly - Ep 264 - Covariant Return Types and Covariant Smart Pointers**

- 引入一个 Covariant_Pointer 结构来实现返回类型是智能指针包裹的 hierarchical type 时候的 covariance
- Covariant_Pointer 的设计有点巧妙

    ```cpp
    template<typename Contained, typename Base = void>
    struct covariant_pointer : covariant_pointer<Base, void> {
        std::unique_ptr<Contained> ptr;
    };

    template<typename Contained>
    struct covariant_pointer<Contained, void> {};

    struct base {
        virtual covariant_pointer<base>& get() = 0;
    };

    struct derived : base {
        covariant_pointer<derived, base>& get() override {
            return ptr;
        }

        covariant_pointer<derived, base> ptr;
    };
    ```

**constexpr Dynamic Memory Allocation, C++20** https://www.cppstories.com/2021/constexpr-new-cpp20/

- C++ 20 引入了一个 transient allocation 的概念，使得一个 constexpr context 里只要 allocation/release 是配对的，就可以合法的使用
- 这个特性使得 constexpr std::string std::vector 这种容器变得可能
- 另外如果需要把 constexpr context 中的结果返回，可以借助 std::array 来传递结果；因为 transient allocation 不能离开自身的上下文，而 std::array 没有这个束缚

\#2

这周终于把 absl/Status 的代码看完了，笔记在：https://kingsamchen.github.io/2024/05/12/src-study-abseil-status-status/

后续有时间的话再看看 absl/StatusOr

\#3

之前有提过想要 disable vagrant 默认的 2222 ssh 端口转发，最近发现不需要禁用，只要直接把自己需要的 ssh 端口转发规则的 id 标为 ssh 即可，即

```
config.vm.network "forwarded_port", guest: 22, host: 14922, id: "ssh"
```

就可以了

---

好了这周就这样，下周见
