---
title: 一周杂记 in Week 1 Nov 2024
categories: CODE-LIFE
date: 2024-11-11 01:12:32
tags: [杂记]
---
本周（11/04 ~ 11/10）是十一月份的第一周，天气开始正式转凉。

## Life

\#1

之前电脑配件都到了，所以改约了周一上门装机。

等到下午三点半，终于上门了。不过还得是专业人员效率高，差不多一个小时就装完了。

因为 AMD 9950X 比较奇葩，所以就算最后临时改成了 GSkill 的 48G x 2，开机自检也花了快十几分钟；而且更蛋疼的是为了避免出问题，进入 BIOS 后我让师傅把频率直接改成 6000Mhz 得了，然后重启又是十几分钟的自检...

然后师傅顺手给我装了一个中文的 Win11 系统，还改了一堆设置；这我作为一个有洁癖的人肯定受不了的，所以在烤机结束基本都没问题之后，我自己用提前做的U盘把系统重装了。

这回装了标准的 Windows 11 Pro 24H2 英文版。不过我事前犯了一个错误，没提前把 ASUS 主板的网卡驱动准备好，导致连接 WiFi 时卡在了连接这一步...还好用自己的笔记本把驱动下好放到了U盘，抢救了一把。

然后就是连续两三天的配置系统和装额外的东西，这部分可以看 {% post_link must-dos-and-rescue-vagrant-vm-after-installing-windows-11 这篇详细说明 %}

\#2

本周观影

- **异形大战铁血战士2 AVPR: Aliens vs. Predator – Requiem** 2/5 保罗安德森真的除了乱改设定喜欢给自己老婆加戏之外水平还是在线的，换了导演这部都拍成山寨B级片了，大段夜间场景还各种暗色调，完全看不清楚画面。出场人物这么多没有一个角色是立住的，包括铁血和混血仔，而且充满了各种B级片恶趣味桥段。难怪之后立马想着重启…

## Work

\#1

**C++ Templates 2nd | 19. Implementing Traits**

- 这一章基本都是实现技法的内容
- 最基础的通过 class specialization 实现的 traits class
- policy class 和 traits 的区别，前者更注重 behavior
- 展示了一些标准库的 traits 其实是如何实现的
- 展示了 functio overloading based SFINAE 和 class partial specialization based SFINAE 来实现一些 type traits 的技法；现在都应该用后一种了，因为比较直观，第一种对应远古早期做法
- 如何让 type traits 更加 SFINAE-friendly，核心是 apply traits 的上下文也应有 SFINAE 保护，避免实例化时出现 hard error
- 一些常用的或者可能会有用的构造常见 traits 的 SFINAE 技法

**CppCon 2022 | C++ MythBusters - Victor Ciura** https://www.youtube.com/watch?v=bcl33-lIC70&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=59

- sprintf 并不快，内部用了 global locale 有锁竞争，换 fmt 保平安
    - 标准库中涉及 locale 的东西都多少有这个毛病
- std::regex 太慢了，性能差，编译久，考虑考虑 CTRE 吧，compile-time automata，cleaner api
- std::optional 要注意 implicit copy 导致的性能问题（记得之前也有一篇 post 提过这个）；尽量不要用 optional 做异常处理机制
- 注意 std::move 的使用（老生常谈了）
- 对于 input stored argument，有时候 pass by value 配合正确使用 move 性能更好（应该也算常识了）
- 总结就是：get to know your tools well

**CppNow 2024 | Rappel: Compose Algorithms, Not Iterators - Google's Alternative to Ranges** https://www.youtube.com/watch?v=itnyR9j8y6E&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=23

- 首先说 ranges 用的模型的问题，例如容易写出 lifetime issue (dangling references)，lazy evaluation & pull model 在很多日常场景下性能不佳
- 然后提出 google 内部的 alternative：rappel，用的 pull model，放弃 pipeline syntax，采用 cps
- 最后 Q&A 有提到在 rappel 出现前 Google 内部的 code guidelines 也是不允许使用 ranges 的

**C++ Weekly - Ep 453 - Valgrind + GDB Together!** https://www.youtube.com/watch?v=pvOYwxsDIJI

- valgrind 可以使用 remote debugging protocol 和 gdb 协作，好处是可以在出现内存问题时自动在 gdb 中断下

**C++ Weekly - Ep 440 - Revisiting Visitors for std::visit** https://www.youtube.com/watch?v=et1fjd8X1ho

- 算是老话题了，variant visitor 的 overloaded idiom…

**Replace std::find_if in 80% of the cases** https://www.sandordargo.com/blog/2021/09/29/replace-find-if-with-any_of-none_of-all_of

- 总结起来就是很多 std::find_if 的使用可以用 any_of/all_of/none_of 来替换，代码更简单语义更清晰

**A Recap on User Defined Literals** https://www.fluentcpp.com/2021/10/08/a-recap-on-user-defined-literals/

- user defined literals 入门教学

\#2

这周因为忙着 fine-tune 新电脑所以一直没有太多时间写代码，争取下周加油 🤣

---

好了这周就这样，下周见
