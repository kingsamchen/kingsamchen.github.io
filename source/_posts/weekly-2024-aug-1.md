---
title: 一周杂记 in Week 1 Aug 2024
categories: CODE-LIFE
date: 2024-08-05 22:24:33
tags: [杂记]
---
本周（07/29 ~ 08/04）是8月份第一周，杭州迎来夏季酷暑，多天处于40℃。

## Life

\#1

周二团建，分两拨，一波去西湖附近的茶庄喝茶打牌，我选了西溪银泰这波，吃的新发现但是太预制菜了不喜欢，不过 xplaying 还可以，虽然性价比一般

抓娃娃花了所有游戏币都没抓到一个，但是射箭还挺有意思，不过确实太耗费体力了，玩了一会儿就累了。

最后和同事玩 PS 上的毛线人游戏，感觉挺有意思的，不过这游戏得俩人玩吧，让我一个人自个儿在家估计就玩不下去了

\#2

周三上午去了极氪家，把修好的车“小心翼翼”的开回了家

这次维修费用都走保险了，不知道明年保险涨多少 🤣

\#3

周三周四周五因为高温都可以在家办公，但是周四因为要去踢球所以还是去了公司。

天气太热还是不要踢球这种剧烈运动了，周四晚上提了快一个小时就感觉全身都累得不行，中间补液补了好几次，最后提前十分钟直接受不了下场了。

这里有个插曲，大概四十多分钟的时候一个同事不小心伤到脚了，后面看起来可能骨头或者肌腱断了，直接喊了120送去了医院 🤦‍♂️

\#4

周六媳妇儿门诊，刚好约了两个发小去转塘那边的西投银泰看死侍。

西投银泰是刚开的店，虽然在转塘，但是因为直接走的紫之隧道，所以反而其实20多公里只要二十几分钟就能到，就当顺带玩车啦。

不过银泰里现在吃的不多，中午随便吃了一点米村拌饭就等着电影开场了。

惊喜的一点是周末的停车费居然是免费的...而且居然车位还比较好找，very interesting...

\#5

- 电影 **菊豆 4/5** 张艺谋怎么越往后水平倒退的这么厉害
- 电影 **死侍与金刚狼 4/5** 给各种狼叔（包括亨利 XD）多加一颗星，没他这片我能看绷。作为xmen/fox超英老粉我还是得说就故事性而言差银护3太多了，虽然玩梗和各种客串量大管饱但是我还是一个观影偏叙事的人。贱贱在这部的表现有点太过了，虽然你是marvel四大话痨但是中间有一些呈现过于刻意。Fox 时代迎来谢幕，期待一些角色在未来的客串 🤣
- 美剧 **the boys s04 4/5** Ryan 这一推直接把 Butcher 推到了黑暗面；我说怎么 Hughie 和 Newman 这么暧昧原来两人是现实情侣；阿祖越来越疯癫了；就等最终季

## Work

\#1

**C++ Templates 2nd | 11. Generic Libraries**

- 写泛型库的时候的一些 tips
- std::invoke 能统一化function object 和 member function
- decltype(auto) 能控制返回类型做到类转发（value or reference）
- 泛型代码里需要取地址应该用 std::addressof
- decval() 只能在 unevaluated context 中使用，并且调用返回的类型要注意可能是引用类型
- 注意模板参数如果被手动实例化为 reference 的话会不会有什么坑（好像平时都没怎么注意个）

**CppCon 2022 | An Introduction to Multithreading in C++20 - Anthony Williams** https://www.youtube.com/watch?v=A7sVFJLJM-A&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=6

- 作者是写 c++ concurrency in action 的大佬，不过看视频感觉有点多动症？
- 先从 amdahl’s law 指出多核环境下需要提升系统的并发度
- std::jthread 以及 std::stop_source & stop_token 用以 cancellation
- C++20引入的一些同步原语：latch, barrier
- async/future/promise 三件套，但是这三个有缺陷所以嘛…
- mutex & RAII-lock，C++ 17 开始 std::scoped_lock 应该是默认选择；std::condition_variable_any 从 20 开始支持传入 stop_token 来 stop waiting
- semaphore for resource counting
- atomic 这部分没啥新东西

**CppCon 2022 | Back to Basics: Templates in C++ - Nicolai Josuttis**

- template 基础教学课，不过涵盖了不少 concepts 和 if constexpr 的内容

**CppCon 2022 | C++ in Constrained Environments - Bjarne Stroustrup** https://www.youtube.com/watch?v=2BuJjaGuInI&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=7

- 虽然主题说的是针对 constrainted environment 的开发，但是绝大多数 tips 都是通用的
- Bjarne 老爷子看来还是倾向于通过 static analyzer 来 enforce 各种 guidelines 以及配合新的 language feature 来做到 memory safety

**C++ coroutines: Defining the co_await operator** https://devblogs.microsoft.com/oldnewthing/20191218-00/?p=103221

- 系列第8篇
- 讲了创建 awaiter 除了前面介绍的实现 await_* 系列函数的类之外还有可以提供 operator co_await
- 一般来说是为某个现有类型提供 free function 版本，以在不修改类型代码的情况下为其支持 co_await

**C++ coroutines: The co_await operator and the function search algorithm** https://devblogs.microsoft.com/oldnewthing/20191219-00/?p=103230

- 系列第9篇
- 讲了 free function of overload for co_await 的一个例子，并且得出一个推论：尽量不要给标准库内建类型重载 co_await operator 否则容易遇到 ADL 的问题

**C++ coroutines: The problem of the synchronous apartment-changing callback** https://devblogs.microsoft.com/oldnewthing/20191220-00/?p=103232

- 系列第10篇
- 这篇相对没那么有意思，讲的 C++/WinRT 存在一个bug，可能会导致 UI thread 的flow co_await 在一个 background thread 上

**C++ coroutines: The problem of the DispatcherQueue task that runs too soon, part 1** https://devblogs.microsoft.com/oldnewthing/20191223-00/?p=103255

- 系列第11篇

    ```cpp
        bool await_suspend(coroutine_handle<> handle)
        {
          m_queued = m_dispatcher.TryEnqueue([handle]
            {
              handle();
            });
          return m_queued;
        }
    ```

    这里有可能 lambda 在Dispatcher 里运行的时候 TryEnqueue 的结果都还没返回

    > Once you arrange for the `handle` to be called, you cannot access any member variables because the coroutine may have resumed before `async_suspend` finishes.
    >

**C++ coroutines: The problem of the DispatcherQueue task that runs too soon, part 2** https://devblogs.microsoft.com/oldnewthing/20191224-00/?p=103262

- 系列第12篇
- 第一次尝试是提供一个 slim event （非常轻量的手搓同步原语）来同步，因为发生的概率很小，所以选择是 lambda 执行等待 await_suspend 执行结束，这个在绝大多数情况下都是默认成立的
- 存储 slim event 的地方这期用了一个 trick，存到了 lambda 里然后利用一个 tracked ptr 再上下文被 coroutine 栈帧移动之后持续更新位置，方便 await_suspend 中调用

**C++ coroutines: The problem of the DispatcherQueue task that runs too soon, part 3** https://devblogs.microsoft.com/oldnewthing/20191225-00/?p=103265

- 系列第13篇
- 答案还真就是直接把 slim_event 放 awaiter 里就可以了。。
- 下一步的目的是去掉 slim_event 带来的 memory barriers

**Can non-overlapping spinlocks deadlock in C++?** https://www.justsoftwaresolutions.co.uk/cplusplus/can_spinlocks_deadlock.html

- 答案是不会，只要两个 spin-lock 没有重叠编译器不会“优化成”永远死锁的情况
- 但是原文这句

    > It can move an arbitrary number of executions of line #3 above line #2 (all of which will see that mutex2 is still true), but not all the executions of line #3.
    >

    比较耐人寻味

\#2

结合 cppcon 2022 里讲 cpp 20 synchronization primitives 的 talk 抽了点时间自己写了一些 samples

算是加深熟悉这些新原语的使用了

代码见这里：https://github.com/kingsamchen/Eureka/pull/26

\#3

周日抽了点时间终于把 线性代数及其应用 1.2 的习题做完了，这样算是能松一口气了

下周再看有没有时间开始看 1.3 的课程内容

---

