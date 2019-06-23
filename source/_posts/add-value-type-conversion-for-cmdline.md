---
title: 为 CommandLine 加上值指定类型转换以及一些扯淡
categories: PROGRAMMING
date: 2019-06-23 14:56:22
tags: [KBase, command-line, abstraction]
---

过去两周抽了点时间给 `KBase::CommandLine` 做了一个[变更](https://github.com/kingsamchen/KBase/commit/e15c81869a9da5422e2012f6b0c4b87be2a1bf0b)，重新设计了 parameter 和 switch 的用户访问接口。

变更的其中一个核心改动是：获取 parameter 或 switch value 时提供了值类型的转换。

有了这个 change 之后，使用者可以通过类似

```c++
auto port = cmdline.GetSwitchValueAs<int>("port", 9876);

auto limit = cmdline.GetParameterAs<long long>(2);
```

的方式直接获取目标值，免去了自己手动将 `std::string` convert 到指定类型的操作。

同时，这两个接口还支持自定义的 data type converter，用户只需要传入一个 function object 就可以定制转换过程。

---

和 Python 的 `argparse`；golang 的 `flag` 乃至 boost 的 `program_options` 这些需要在一开始“定义”程序使用的 switches(flags) 及其绑定的变量的库不同，`CommandLine` 对 switches 的使用更多的是一种辅助探测，非侵入式的。

这种设计是有意的，因为这符合我个人的设计哲学：低层次的设计尽量避免对高层次的约束依赖。

Golang 的 flags 在普通场景下看似使用便利，但是如果你在多个组件里定义了同一个名叫 `conf` 的flag，程序就会因为 _redefined flags_ 而导致 panic。更不用提 panic 时甚至不会给出重复定义的上下文，只能靠你自己一个一个排查。

而避免这种问题的发生的唯一方法就是，人为的规定只允许程序主模块（`func main()`）内才能定义 flag，第三方包千万不能定义flag。

如果做不到这点，哪怕不发生 redefined flags 的问题，你的程序也会因为引入了第三方包而“自发”的获得了这些 flags 的定义。

Python 的 `argparse` 设计上坑比 `flag` 稍少，但是在一些复杂的需求上也会让你有一种如鲠在喉的感觉。

本质上，这些库充当的都是 controller 或者 manager 的角色，为了完成他们的定位，不可避免会通过提供一些高层抽象来完成建模；但是 commandline 的作用大部分时候是给主程序提供支持，作为基础库，怎么可能知道主程序对命令行有什么样的需求呢？

灵活性和易用性绝大多数的时候都是相互冲突的。

抽象虽然能够简化细节，做到 selective ignorance，但是同时也增加了约束，这部分约束也是抽象的代价之一。