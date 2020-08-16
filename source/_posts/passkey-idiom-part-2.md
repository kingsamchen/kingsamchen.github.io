---
title: 再谈 passkey idiom
categories: PROGRAMMING
date: 2020-08-16 16:18:35
tags: [cpp, passkey, explicit]
---
我在 {% post_link passkey-idiom-for-make-functions Passkey Idiom %} 这篇文章中介绍了如何使用 passkey idiom 以及使用需要的注意项。

其中最强调的就是 `Token` 类的 ctor 必须**同时满足** 1) `private access level` 2) 存在显式定义；否则用户可以使用

```cpp
Widget w({}, 0);
```

绕过。

其实这里我们还可以将 `Token` 的默认构造函数标记为 `explicit` 来解决；即

```cpp
class Widget {
    class Token {
    private:
        explicit Token() = default;
        friend Widget;
    };
    ...
};

Widget w({}, 0);    // error: cannot convert argument 1 from 'initializer list' to 'Token'
```

因为前面通过 `{}` 构造一个 `Token` 对象实际上使用了 [_copy-list-initialization_](https://en.cppreference.com/w/cpp/language/list_initialization)

注：根据 reference 文档，如果 `class T` 是一个 aggregate，那么 list initilization 的 effect 就是 perform aggregate initialization；所以这里和前面说的 aggregation initialization 并不矛盾。

将构造函数标记为 `explicit` 可以屏蔽/阻止使用 copy-list-initialization，即这里的使用场景。

为了便于记忆可以认为 `explicit default constructor` 会阻止 `{} -> class T` 的创建。
