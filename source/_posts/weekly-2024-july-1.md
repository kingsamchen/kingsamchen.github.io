---
title: 一周杂记 in Week 1 July 2024
categories: CODE-LIFE
date: 2024-07-08 21:49:04
tags: [杂记]
---
本周（07/01 ~ 07/07）是七月份的第一周，梅雨季结束之后直接进入酷暑，每天气温都是30℃以上，白天大部分甚至都在35℃以上...

## Life

\#1

因为周一还在下雨，所以周二上午定着太阳把电驴推到了店里修电瓶。

老板一顿操作猛如虎，发现没啥效果，最后直接给我换了一个“看起来像新的”的电瓶，然后一直强调给我说不要用到电量低于20%，二十多的电就要冲，低电量使用容易引发电瓶的电路保护。

然后店里这个电路保护不太好 override 只能寄回去。

另外有个插曲，周三晚上把电瓶充满后，周四上午发现启动后仪表盘居然不现实电池容量了...

本来想晚上再去店里修一下结果等到下班之后这问题有自己好了...就很离谱

\#2

周二的时候物业管家姐姐问我有没有意向领养小猫咪，她在小区捡到了一直蓝白英短，好几天了也没人上门报失，问了业务也没人说丢了。

我说媳妇儿不让养 Orz 但是可以帮忙问一下公司同事；再得知小猫不怎么吃猫粮之后我说家里还有猫罐头，我给你几个看看小猫是不是有点挑食。

花了十分钟从办公室回到家后拿上罐头到物业办公楼，发现这小猫确实有点憨...开了一个罐头这猫立马蹭过来吃了起来，看来确实是挑食

周三晚上的时候管家姐姐说找到领养了，她同事的男朋友要收养，猫包猫砂盆都准备好了；我一听这好呀，小家伙也不用流浪了直接有家咯

\#3

周日不明原因发烧了，非常难受，但是又比之前新冠和甲流好一些。

测了两个试剂板，发现都不是...

\#4

本周观影

- **克莱门蒂娜 Clementina** 3/5 感觉是近年来看过比较独特的一类电影，生活化慢节奏叙事，没有故事主线，就像那种日常DV摄影的作品，BGM 有点意思

## Work

\#1

本周学习

**C++ Templates 2nd | 6. Move Semantics and enable_if<>**

- 标准移动 + 完美转发，以及使用 SFINAE（enable_if）来限制模板参数类型
- 最后提了一嘴 concepts

**C++ Templates 2nd | 7. By Value or by Reference?**

- 这个问题确实值得一章
- 没有特殊情况其实建议 pass by value，因为可以自动 decay 来适应 string literal 和 raw array
- 真有性能要求再考虑 pass by reference，但是要注意不会自动 decay 的问题
- forward reference 的时候要小心 T 被推导为 reference 如果恰好返回类型又是 T 就有可能有坑，可以手动 remove_reference trait 或者干脆直接用 auto 作为返回类型来 always decay

**CppCon 2021 | How Can Package Managers Handle ABI (In)compatibility in C++? - Todd Gamblin**

- C++ 包管理会面临的问题（ABI兼容性，多语言混编，依赖间接依赖的冲突）
- conan 的一些做法
- spack 的一些做法，以及 SAT 和 ASP 两种作为 solver 算法的优缺点（作者是 spack 的开发人员之一）

**CppCon 2021 | Units Libraries and Autonomous Vehicles: Lessons from the Trenches - Chip Hogg**

- 基于类型的 units library 对业务代码的促进作用（例子又是各种长度的量纲计算…）

**CppCon 2021 | The Unit Tests Strike Back: Testing the Hard Parts - Dave Steffen**

- 如何处理 flaky test, non-hermetic tests
- 处理 legacy code，举例某个 vector 没有暴露出 capacity 函数，如何尽量减少影响下增加对 capacity 的测试（一堆骚操作）
- code is hard to test because of its interface and general non-testable-ness

**Markus Klemm "C++20 Coroutines, with Boost ASIO in production: Frightening but awesome”** https://www.youtube.com/watch?v=RBldGKfLb9I

- 类似之前讲 asio + coroutine 网络编程的 talk，不过那个 talk 讲的更加具体一些，例子也基本都是 tcp server programming 部分的

**Create a future with coroutine in C++** https://cpp-rendering.io/create-a-future-using-coroutine/

- 这篇 post 比 moderncpp 那个 resume work on another thread 更完善一点，尤其在 awaiter 的展示手法上
- future(awaitable) → co_await on future → awaiter(提供 await_ 三件套函数）

**All C++20 core language features with examples** https://oleksandrkvl.github.io/2021/04/02/cpp-20-overview.html

- feature 一览，写的比较 overview

**Rounding floating point numbers and constexpr** https://vorbrodt.blog/2021/04/04/rounding-floating-point-numbers-and-constexpr/

- 给定一个浮点数和需要保留的精度位数，将浮点数 round 到这个精度
- 文末有个打表法，吊炸天

\#2

最近在给公司的项目写一个通用的 http rpc client，但是对于某些对应 HTTP GET 请求的 request struct 没法序列化成 JSON，相反需要转换为 query string 完成请求。

所以就想着参照之前给 nlohmann json 做的 JSON SERDE 宏，做一套类似的生成 `cpr::Parameters` 的宏

代码不复杂，并且因为字段类型强制都是 string，所以需要做的 trick 少了一些 https://github.com/kingsamchen/Eureka/pull/24

PS：如果你的项目用 fmtlib 的话，stringify 直接用 `fmt::to_string()` 就行了

PPS：这次又一次遇到了 MSVC 下 `__VA_ARGS__` 展开不正确的问题，这次 mark 一下免得以后忘了

- https://stackoverflow.com/questions/5134523/msvc-doesnt-expand-va-args-correctly
- https://learn.microsoft.com/en-us/cpp/preprocessor/preprocessor-experimental-overview?view=msvc-160

---

好了这周就这样，下周见
