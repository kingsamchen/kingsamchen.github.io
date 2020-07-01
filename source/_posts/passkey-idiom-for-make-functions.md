---
title: Passkey Idiom
categories: PROGRAMMING
date: 2020-07-02 00:28:19
tags: [cpp, make_unique, make_shared, passkey]
---
如果出于某些目的希望将某个类的构造函数设置为 private，并提供工厂函数 `Make()` 创建最终对象；工厂函数中通常会使用 `std::make_unique()` 或者 `std::make_shared()` 来创建由对应智能指针托管的对象。

但是因为构造函数被设置成了 private，因此这两个 make 函数**内部**创建对象会编译失败。

如果我们实在不希望使用 `new` 去构造对象，可以使用 passkey 手法规避无法编译的问题。

```cpp
class Widget {
  class Token {
   private:
    Token() {}
    friend Widget;
  };

 public:
  static std::shared_ptr<Widget> Make() {
    return std::make_shared<Widget>(Token{}, GenerateId());
  }

  Widget(Token, int id) : id_(id) {}

 private:
  static int GenerateId();

  int id_;
};
```

上面例子使用的是 `make_shared()`，`make_unique()` 也是类似。

需要注意，不管 `Token` 类是否是 `Widget` 的 private 成员，`Token` 自身的构造函数必须满足

- private access level
- 显式定义（即不能使用 `= default`）

否则会存在一个漏洞，即下面的代码能够编译通过

```cpp
class Widget {
    class Token {
    private:
        Token() = default;
        friend Widget;
    };
    ...
};

Widget w({}, 0);    // Compiled.
```

因为 `{}` 被认为是 aggregate initialization，进而无视 default constructor 的访问级别。

显式定义构造函数可以避免 `{}` 被“理解”为 aggregate initialization。

## Misc

使用 `make_unique` 的最大优点是可以做到异常安全；对于这样的代码

```cpp
void HeavyRender(std::unique_ptr<Widget> w, Foobar&& fb);

// If an exception is thrown from `AcquireFoobar()`, the newed memory would leak
HeavyRender(std::unique_ptr<Widget>(new Widget()), AcquireFoobar());
```

编译器无法保证函数 `AcquireFoobar()` 执行的时候 `new` 返回的地址已经被用于构造 `unique_ptr<Widget>`；因此如果此时前者抛出了一个异常，动态分配的对象就泄露了。

不过通常来说，只要记住涉及 new 的语句只有一个 side-effect 就可以很大程度避免这个问题。

对于 `make_shared()`，除了异常安全的优势之外，内部只做一次内存分配带来的性能提升可能是大多数人使用这个函数的初衷。
