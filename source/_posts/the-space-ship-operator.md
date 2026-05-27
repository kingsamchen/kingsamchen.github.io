---
title: The Space-ship Operator <=>
categories: PROGRAMMING
date: 2026-05-27 22:57:56
tags: [cpp, operator<=>]
toc: true
---

## Totality & Interchangeability

这两个点是区分 strong ordering, weak ordering 和 partial ordering 的关键

***totality*** 是说，给定 a 和 b

- a < b
- a == b
- a > b

一定有一个成立

不具备 totality 的类型例如

- 集合 `{1, 2}` 和 `{2, 3}`，二者即不相等，也没有其中一个是另一个的子集
- 浮点数中某个值是 NaN，直接无法比较

---

Interchangeable 则是说：如果 a == b 满足，则 a 和 b 可以互相替代，二者可以视作完全一样

- 满足的例子是 integer 里，a 和 b 都是 42
- 不满足的例子是 case-insensitive strings，例如 `"hello"` 和 `"HELLO"`；不考虑大小写二者相等，但是二者不能互相替换

## Ordering

基于上面的定义：

|  | **Totality** | **Interchangeable** |
| --- | --- | --- |
| Strong Ordering | Hold | Yes |
| Weak Ordering | Hold | No |
| Partial Ordering | May Not Hold for some values / UnOrdered |  |

另外需要注意：

- strong ordering 的 equivalent 和 equal 是等价的
- weak ordering 只有 equivalent 语义

不难理解，更强的 ordering 总是 imply 弱一些的 ordering，所以有

```cpp
std::strong_ordering s  = std::strong_ordering::less;
std::weak_ordering   w  = s;   // OK: strong → weak
std::partial_ordering p = s;   // OK: strong → partial
std::partial_ordering p2 = w;  // OK: weak → partial
// std::strong_ordering s2 = w; // ERROR: cannot go upward
```

## 习惯用法

```cpp
struct Foobar {
    int    a;
    double b;
    std::string s;
    auto operator<=>(const Mixed&) const = default;
    // Deduced return type: std::partial_ordering because b is partial ordering
};

Foobar lhs{...};
Foobar rhs{...};

// Compiler will rewrite comparison operators using <=> for the class Foobar
bool r1 = lhs < rhs;
bool r2 = lhs == rhs;
bool r3 = lhs >= rhs;
```

- 返回类型 auto 让编译器自己推导，有特殊需求也可以写清楚返回类型
- member const function，和传统实现成 friend function 不太一样
- 用 `= default` 让编译器自动生成，生成的实现会比较每个成员

### 限定指定成员比较

如果比较时候需要指定成员，例如跳过 mutex 等成员，则可以

```cpp
struct ID {
    int id_number;
    auto operator<=>(const ID&) const = default;
};

struct Person {
    ID id;
    string name;
    string email;
    // lets do it
};
```

逐个成员手动比较

```cpp
std::strong_ordering operator<=>(const Person& other) const {
    if (auto cmp = id <=> other.id; cmp != 0)
        return cmp;
    return name <=> other.name;
}

bool operator==(const Person& other) const;
```

甚至在好几个 member 时看起来更规整的

```cpp
std::strong_ordering operator<=>(const Person& other) const {
    if (auto cmp = id <=> other.id; cmp != 0)
        return cmp;
    if (auto cmp = name <=> other.name; cmp != 0)
      return cmp;
    return std::strong_ordering::equals;
}

bool operator==(const Person& other) const;
```

或者老 trick，借助 `std::tie()`

```cpp
    std::strong_ordering operator<=>(const Person& other) const {
        return std::tie(id, name) <=> std::tie(other.id, other.name);
    }

    bool operator==(const Person& other) const;
```

<aside>
⚠️ 如果手动实现 `operator<=>` 则应该也手动实现 `operator==`

</aside>

## Compiler Rewrites & Synthesized Operators

如果一个类提供了 `operator<=>` 但是没有提供其他操作符，编译器会特殊处理这些操作符。

### 0x0 Rewrites `operator<` / `operator<=` / `operator>` / `operator >=`

规则如下：

- 后者这四个操作符的实现会直接调用 `operator<=>` 并利用它的结果返回
- 如果用户提供了某个操作符的实现，则编译器会跳过为其生成 rewrite-implementation

    ```cpp
    #include <compare>
    #include <iostream>

    struct F {
        int x;

        std::strong_ordering operator<=>(const F& other) const {
            std::cout << "<=> called\n";
            return x <=> other.x;
        }

        bool operator<(const F& other) const {
            std::cout << "< called\n";
            return x < other.x;
        }
    };

    int main() {
        F a{1}, b{2};
        bool r1 = a < b;    // Prints "< called"  — your operator< wins
        bool r2 = a > b;    // Prints "<=> called" — synthesized from <=>
        bool r3 = a <= b;   // Prints "<=> called" — synthesized from <=>
    }
    ```


### 0x1 Implicitly Synthesized `operator==` / `operator!=`

这两个操作符的处理稍有不同：

- 如果定义了 defaulted operator<=> 则编译器会自动合成 defaulted operator== 和 defaulted operator!=
- 反之，如果用户实现了 non-defaulted operator<=>，则不会再自动合成 == 和 != 操作符

这里是直接合成（synthesize/generate）对应的操作符，而不是基于 operator<=> 重写（rewrite）操作符，因为 `operator==/operator!=` 的实现对于相当一部分类型，例如容器类型，通常都有优化：只要元素个数不一样就一定不一样，不用再逐个比较元素

而大小关系的比较相对来说成本较高

规则 [](https://www.notion.so/36cc9c82867b80ceaabbd2095f319ea2?pvs=21) 也是基于此

### 0x2 Defaulted operator<=> 的要求

不当语言律师研究那些犄角旮旯，只说最常见的日常情形。

参数必须要是类型本身

```cpp
struct Number {
    int value;

    // Invalid parameter type for defaulted three-way comparison operator;
    // found 'int &', expected 'const Number &'
    // auto operator<=>(int &) const = default;
};
```

member 里如果有不支持 operator<=> 的成员则类自身这个也会变成 deleted

## Reversed Rewritten Candidates

如果一个类实现了 `operator<=>` 那么对于 `a < b` 这样的比较操作，编译器不仅会考虑 `a < b` 还会考虑改写成 `b > a` ，解决以前操作符重载作为成员函数时，无法做到对称的问题

```cpp
TEST_CASE("Reverse rewrite candidates") {
    struct Number {
        int value;

        // Suppress implicit conversion
        explicit Number(int v) : value(v) {}

        auto operator<=>(int n) const {
            return value <=> n;
        }

        bool operator==(int n) const {
            return value == n;
        }
    };

    Number n{42};
    CHECK(1 < n);
    CHECK_FALSE(1 > n);
    CHECK(42 == n);
}
```

<aside>
💡 RRC 并不针对 operator<=>，其他的比较运算符也能应用到 RRC，例如例子里的 operator==

</aside>

<aside>
⚠️ 只有比较运算符才有 RRC，其他运算符因为不能保证交换律所以不提供

</aside>

## Hidden Friend Impl

因为比较运算符自 C++20 支持 Reverse Rewrites Candidates，所以对比较运算符重载实现来说，大部分情况下没必要用以前的 hidden friend 方案了

除非需要比较 tricky 的 implicit conversion

```cpp
class Number {
    int value;
public:
    Number(int v) : value(v) {}  // implicit

    friend bool operator==(const Number& lhs, const Number& rhs) {
        return lhs.value == rhs.value;
    }
};

struct Foo {
    operator Number() const { return Number{0}; }
};

Foo f;
f == 1;   // Both sides convert to Number
1 == f;   // Same as above
```

不过这种需求有点危险，一不小心容易踩坑

另外就是其他的运算符不支持 RRC，所以还是要用到 hidden friend
