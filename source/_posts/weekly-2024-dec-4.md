---
title: 一周杂记 in Week 4 Dec 2024
categories: CODE-LIFE
date: 2024-12-29 23:27:26
tags: [杂记]
---
本周（12/23 ~ 12/29）是十二月的第四周，也是最后一周；下周三开始就是新年元旦。

## Life

\#1

周一晚上媳妇儿开了空调睡觉，主机运转声隔一会儿响一下外加冷凝管中滴水声，直接导致我凌晨三点多被惊醒后就失眠睡不着了。

吃了粒褪黑素依然没有什么用，脑子里还是一直回放着空调机噪音。

讲道理夏天开着冷空调睡觉的时候噪音都没这么大

在床上挣扎到四点五十多终于受不了了爬起来到书房打开电脑打游戏...一直到6:50多媳妇儿起床洗漱出门后才回床上开始补觉

缺觉真的挺可怕的，补觉到10点整爬起来 syncup，整个人脑子都是懵，感觉自己都不知道自己说了啥。

10:30多 sync 结束后立马回到床上继续补觉，再醒来已经是12:40多了，嘉明手表都提示说午觉睡多了（按照手表算法，午觉睡了50多分钟）...

中午吃了 KFC 之后感觉人是有缓过来一点，下午起码能正常上班了...

缺觉才发现有充足的睡眠真的是一件幸福的事情

\#2

周四晚上正打着 darksiders 突然发觉游戏很卡，退出游戏后发现系统进程和 vmware 的管理进程占满了CPU。

一脸疑惑着打开了资源管理器，突然发现，欸，我的D盘怎么不见了？

这次新电脑挂了三个盘，C、D各自都是990Pro 4T；C存系统和程序应用，D盘存开发相关的东西，包括 vagrant/vm

一脸懵逼中选择重启，然后发现系统卡在启动黑屏了，看了一眼主板发现报错误码15

联系了之前装机师傅他说可能主板坏了，约了第二天来修

过了一会儿越发觉得难受，这电脑才装完俩月不到主板就坏了？

掏出笔记本查了一下ASUS的手册发现code 15是pre-memory initialization，没懂具体啥意思，放 google 搜一下。

看了几个 posts 和 youtube 视频发现这个码就是代表主板需要进行 memory training，和第一次装DDR5内存需要很长自检那个是一回事

那既然如此我就等一等呗。开机等了快十分钟果然状态码变了，然后顺利进入了系统...🤣

打开资源管理器发现三个盘都在，而且看 system monitor 各项指标也正常啊...

最后想了一下要不刷一下 BIOS 吧。然后，好家伙，ASUS给 x870e hero 这几乎每个月都出了一个 update...

又折腾了半小时后把 BIOS 刷新了，然后神奇的发现之前可以选 EXPO 这回只能 DOCP 了，但是好像 DOCP I 都可以把 DDR5 上 6400Mhz了。用了几天目前没发生啥问题

第二天和师傅说了这个事，他说那就不用上门了，估计就是 AMD 和 DDR5 的兼容性毛病 🤬

\#3

周日下午一阵奋战，Darksiders I Warmastered 终于全成就解锁

![](img/image_2024-12-29_15-56-18.png)

\#4

周日晚上和媳妇外出买懒人沙发和运动鞋，一开始选了西溪银泰，因为那边有迪卡侬和MUJI

但是逛了一圈发现鞋子媳妇儿觉得太丑，MUJI没有懒人沙发可以供体验。

讨论了一下，开车换到良渚的永旺商城，19年年底买 Nitori 的地方看看，那边不仅有 Nitori 也有 MUJI 还有好几家鞋店。

然而最后的结果是：

- MUJI 的一个座椅媳妇儿觉得还可以，但是不够惊艳/舒服
- Nitori 的一个小沙发很舒服，但是她觉得有点大了，而且颜色不好看...
- 看了几家鞋子店要么舒服的太丑，好看的穿着难受

最后两手空空又回家了 🤡

还好也就自己开车来回方便，要是还是以前那种坐地铁或者打车，估计我真的会绷不住骂人

\#5

本周观影

- **龙卷风 Twisters** 3/5 节奏不太行，开头紧凑最后半小时紧张刺激但是中间一个多小时简直让人无聊到想睡觉。视效灾难片要探讨深度也不应该这么搞，另外硬要凑一块的结局还是有点老套的
- **飞黄腾达 The Apprentice** 3.5/5 主演的表演贡献不错，Sebastian 模仿川普的神态和说话嘴部动作包括咬舌都很传神，Roy Cohen的霸气内露把握得很好，那个眼神真能威慑人。然后是缺点，如果主创团队还是以偏见的状态去拍的话，呈现的效果不管怎么样都上不去的

## Work

\#1

**CppCon 2022 | GitHub Features Every C++ Developer Should Know - Michael Price** https://www.youtube.com/watch?v=Mo8MeVzzdE8&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=74

- Github CI pipeline 的搭建（这部分有点浅了，不过作为一个宣传点倒是还可以）
- codespace 这种 web IDE 的介绍

**CppCon 2022 | Lightning Talk: Cute Approach for Polymorphism in C++ - Liad Aben Sour Asayag** https://www.youtube.com/watch?v=Y0B5AkvBL2w&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=75

- 这个短 talk 出人意料地有点意思
- 频繁调用虚函数 handle 比较慢，一个小 trick 是直接把 range handling 函数处理成 virtual，内部具体的 handle 函数为普通函数，这样 virtual call 只有一次了
- 另外一个有意思的点是，作者最后利用 type-erasure 的方式将 specific handler “注入”到 general handler 中，既不影响性能，又可以做到 separation of concerns
- sample 大概长这样
    
    ```cpp
    using element_t = int;
    
    struct handler_base {
        virtual ~handler_base() = default;
    
        virtual void handle(std::span<element_t> elements) = 0;
    };
    
    template<typename HandlerImpl>
    struct handler_general : handler_base {
        void handle(std::span<element_t> elements) override {
            for (auto e : elements) {
                impl.handle(e);
            }
        }
    
        HandlerImpl impl;
    };
    
    struct handler_impl_a {
        void handle(element_t e) {
            fmt::println("handle with impl_a on {}", e);
        }
    };
    
    struct handler_impl_b {
        void handle(element_t e) {
            fmt::println("handle with impl_b on {}", e);
        }
    };
    
    int main() {
        std::vector<element_t> v{1, 3, 5, 7, 9};
    
        // use lambda to mimic factory-like function.
        std::unique_ptr<handler_base> handler = []() {
            return std::make_unique<handler_general<handler_impl_b>>();
        }();
    
        handler->handle(v);
    
        return 0;
    }
    ```
    

**CppCon 2022 | Back to Basics: Master C++ Value Categories With Standard Tools - Inbal Levi** https://www.youtube.com/watch?v=tH0Z2OvHAd8&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=73

- 还是 lvalue rvalue 然后伴随 reference 那些事
- 毕竟是 back to basics 系列嘛

**CppNow 2024 | Dependency Injection in C++ - A Practical Guide - Peter Muldoon** https://www.youtube.com/watch?v=kCYo2gJ3Y38&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=49

- 分享了 C++ 里 DI 得常用方法，明确不建议再使用 link-time shim 和 setter DI
- 复杂对象内部结构抽象成小对象然后以正确的 DI 方式引入得技巧值得一学
- 后面放了一些具体得 sample 来示范如何做 DI
- 整体而言推荐观看

**CppNow 2024 | Keynote: C++ Painkillers for C++ Developers - The Evolution of C++ Tooling - Anastasia Kazakova** https://www.youtube.com/watch?v=sxWe9KzYQSI&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=51

- 几乎把最近几年 C++ 在 tooling 方面的改进都讲了一遍，还是非常推荐的

**C++ Weekly - Ep 459 - C++26's Saturating Math Operations** https://www.youtube.com/watch?v=XNMnQOFrEIY

- 介绍 26 引入的 https://en.cppreference.com/w/cpp/numeric#Saturation_arithmetic_.28since_C.2B.2B26.29
- 简言之这个类别下的计算可以帮你处理 overflow/underflow 得情况并且在发生溢出的时候返回最大/最小得边界值

**C++ Weekly - Ep 420 - Moving From C++17 to C++20 (More constexpr!)** https://www.youtube.com/watch?v=s2XWAxbxk9M

- 对一个现有老项目的 C++ 20 部分改造
- 这个 talk 感觉 illustration 的效果一般，作为观看者找不到啥重点

**C++ Weekly - Ep 419 - The Important Parts of C++23** https://www.youtube.com/watch?v=N2HG___9QFI

- 介绍 C++ 23 引入的 notable feature
- 之前都没注意到 std::expected 可能会阻碍 RVO…

**C++ Weekly - Ep 418 - Moving From C++14 to C++17** https://www.youtube.com/watch?v=yL0DWa2LxNU

- 现有代码从 14 迁移到 17
- 主要用到的是新的 attributes，string_view 和 from_chars

**Converting integers to fix-digit representations quickly** https://lemire.me/blog/2021/11/18/converting-integers-to-fix-digit-representations-quickly/

- 用一些 trick 将 16位数字转换成字符串的快速方法
- small table 和 big table 是最大赢家…如果真的有需求，直接把 small table 那个代码抄一下🤣

**Virtual Inheritance in C++** https://mariusbancila.ro/blog/2021/11/16/virtual-inheritance-in-c/

- 就是非常传统的介绍多继承/虚继承的文章，没什么新意
- 我就有点好奇的是这都哪一年了怎么还写这个…

**A Close Look at a Spinlock** https://blog.regehr.org/archives/2173

- 传统朴素的 spinlock 实现和支持 fairness 的 ticket spinlock
- 然后重点分析了 linux kernel on ARM 的类似 ticket spinlock 的实现

**C++20: Heterogeneous Lookup in (Un)ordered Containers** https://www.cppstories.com/2021/heterogeneous-access-cpp20/

- 14 对 ordered container 支持了 heterogenous looup，20 之后 hashed container 也支持了这个功能，并且未来会有更多的操作支持 heterogeneous
- 核心是 comparator/hasher 需要内部定义 `is_transparent` tag type，才能启用；hashed containers 除了 hasher 之外 key comparator 也需要支持，会多一步
- 标准库中 `std::less<>` 和 `std::equal_to<>` 这些 specialization for void 都定义了这个 tag

**正确使用cpu提供的TSC** https://zhuanlan.zhihu.com/p/437178265

- 从这文章上看，现代CPU里直接使用 RDTSC 应该都是有坑的了，没法保证频率不变和可靠性
- Intel 为了这个问题提供了一些增强，比如不会受到 C-State 影响的 nonstop tsc，还有频率不会随着CPU自身频率变化的 invariant tsc
- 但是具体的 freq 还是需要用特殊的方式获取

\#2

这周得知 US 那边好几个混子都 promoted 了，心态有点崩，并且一心忙着全成就解锁

所以，写代码啥的下周再说吧。刚好下周也进入我自己的休假周期了。

---

好了这周就这样，下周见
