---
title: 一周杂记 in Week 4 July 2024
categories: CODE-LIFE
date: 2024-07-29 23:06:07
tags: [杂记]
---
本周（07/22 ~ 07/28）是7月份的第四周，也是最后一周。下周四就进入8月份了。

## Life

\#1

和发小温总约了周一晚上杭州之翼影院的《永远的托词》，晚上7:30的电影我一开始想着 6:30 出门再怎么也到了，毕竟开车只有十公里。

结果没想到6:30出门之后直接遇到了下班高峰，从文一隧道开始堵，过了之后又再德胜快速路堵上了，好不容易过了德胜快速路秋实高架也堵了一段...到了影院时候已经7:20了，所以急急忙忙地找停车位。

转了一圈终于发现一个，但是车位感觉不是很好停，而且车位附近前段路因为用铁栅栏直接封锁了，001的倒车空间肯定是不够的。

但是电影马上开始了，所以我也不想再调个头倒车，打算勉强试试。

结果这一试直接一个不注意倒车的时候把车屁股蹭到墙上了，划伤了车尾一个区域的车漆...

但是没办法先去看电影吧，电影还行，下面说，但是观影期间我时不时走神想到刚才这个事故...

看完电影后采访导演期间我就先走了，回到车库之后先给保险打了电话出险。但是等了一段时间勘察员说他有其他事情要处理来不了，让我按照要求拍几张照片给他。

拍完之后就回家了，回家路上还算顺利，至少我路上还没出过事故...

周二一早我问了极氪家工作人员有没有上门取车维修的服务，告知没有免费服务...那只能我自己开车到极氪家了。

还好最近的一家离我家只有4-5公里

到了之后对接的人员突然想起来说你是不是有张上门取车券的，打开App卡包一看确实有... WTF 算了下次再说吧

另外一个工作人员是直接对接我的保险车损，初步勘察之后说因为现在处理的车比较多，估计要一周到10天才能修好。WTF again

周二晚上和同事健身结束回家路上工作人员联系我说车漆补一下还好，主要是有个底下的金属装饰板有磕伤，这个只能换，整个维修大概要3K出头，尽量走保险。

😭 人生第一次事故和出险发生在提车3个半月之后

经过这次事故，我总结了一下

- 以后停车的时候不能再听音乐，会容易走神
- 停车的时候先想清楚，确保一开始的方向和空间选择是正确的
- 慢停，不能着急，否则360延迟或者容易不注意

\#2

媳妇儿说主卧浴室的窗户遮光窗纸脱落的面积更大了，需要更换

于是淘宝下单了一家，周日到了之后下午就和媳妇儿研究怎么更换。

纱窗拆卸还算比较简单，主要窗纸粘贴需要依靠肥皂水，而且贴上之后需要用刮板刮出多余的水和空气，这个是技术活

最蛋疼的是卫生间那个地方空间有限，梯子因为马桶挡着只能再比较靠外的一个位置放着，我粘贴和刮的时候全程非常累，一直冒汗

所幸整体操作下来没啥问题，窗纸也完美的换了一张新的，图案这次选了富贵竹，不过实物感觉比如宣传图片的好看

\#3

这周看了电影 **永远的托词** 4/5 我觉得日本电影比较擅长细腻一些的家庭感情和生活化的话题。生活往往就这样，习惯了平淡了没感觉了无所谓了。发生变故之后慢慢地才发现一切都乱套了。两个小孩对幸夫来说不只是重塑了他的生活更重要的是拯救了他的生活观。

下齐 The Boys S04 的字幕之后也开始看 the boys 了

## Work

\#1

**C++ Templates 2nd | 9. Using Templates in Practice**

- 这章主讲 template code compilation model，就是现在我们看到的 inclusion model
- 以及一些编译相关的 tips，比如 pre-compiled headers

**C++ Templates 2nd | 10. Basic Template Terminology**

- 术语科普节
- 对于类、函数等这种模板关联的应该称之为 类模板 class template，因为本质上他们还是模板 .etc
- tempalte parameters are initialized by template arguments （这个解释我觉得很适合记忆）

**CppCon 2022 | Back to Basics: C++ API Design - Jason Turner**  https://www.youtube.com/watch?v=zL-vn_pGGgY&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=3

- 主旨：make your api hard to use wrong
- nodiscard C++ 20 开始可以应用到构造函数，很早之前 chenshuo 提到的 std::lock_guard{std::mtuex} 这种错误就不需要再用 macro-trick 来解决了
- use consistent eror handling
- 总结下来就是接口要尽可能提供明确的语义

**CppCon 2022 | How C++23 Changes the Way We Write Code - Timur Doumler** https://www.youtube.com/watch?v=eD-ceG-oByA&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=4

- deducing this
    - 替代 CRTP 并且更不容易误用
    - 可以容易优雅地实现 recursive lambda
- std::expected<value_type, error_type>
    - 另外一种错误处理机制…
    - 不过 expected 的好处是 error_type 可以自定义，所以 error_code 也可以放，甚至可以用 `std::variant<>`
- std::mdspan
    - non-owning multi-dimensional array view，提供类似 fortran 的数学计算体验
- std::print
    - 以后终于有新的 hello world 的方式了，并且对 Unicode 专门做了支持

**CppCon 2022 | Using Modern C++ to Eliminate Virtual Functions - Jonathan Gopel** https://www.youtube.com/watch?v=gTNJXVmuRRA&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=5

- 做法是利用 concept + std::varaint + tuple 来模拟，会用到不少的 template tricks
- 总结下来是可行但是实用性不是很高
- 未来的方向不如考虑一下 dyno 或者微软那个 proxy lib

**CppCon 2022 | Can C++ be 10x Simpler & Safer? - Herb Sutter** https://www.youtube.com/watch?v=ELeZAKCN4tY&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=2

- Herb Sutter 之前推销的 cpp2 的 cppfront 的 live demo
- 我觉得很多想法还是不错的，尽可能简化现有的 CPP 的复杂度，但是我总觉得现在这步子有点迈大了，弄不好容易扯到蛋。
- 用来处理 low-level 需要时常 fine-tune 的工具再怎么也简单不到哪里，比如 Rust；又想表达上简单易用又想具有灵活性和 zero-cost abstraction 我觉得不太现实，先继续观望吧。
- 不过拿 cpp2 作为一个新 language core 试验田的话到应该是不错的想法

**C++ coroutines: Getting started with awaitable objects** https://devblogs.microsoft.com/oldnewthing/20191209-00/?p=103195

- Raymond Chen 讲 coroutine 系列开篇，一开始就从 co_await 入手的一看就是实干派
- 而且直接讲的如何将 coroutine 切换到其他线程执行

**C++ coroutines: Constructible awaitable or function returning awaitable?** https://devblogs.microsoft.com/oldnewthing/20191210-00/?p=103197

- 系列第二篇
- 用 struct/class 实现 awaitable 和通过函数返回内部的 awaitable 的对比
- 各有优劣，函数版本可以通过重载来返回不同类型的 awaitable，但是如果返回的是同一个类型，那就有点 duplicate

**C++ coroutines: Framework interop** https://devblogs.microsoft.com/oldnewthing/20191211-00/?p=103201

- 系列第三篇
- 展示了一下 `coroutine_handle::address()` 和 `from_address()` 的用法

**C++ coroutines: Awaiting an IAsyncAction without preserving thread context** https://devblogs.microsoft.com/oldnewthing/20191212-00/?p=103207

- 系列第四篇
- 新东西不多，主要是结合 C++/WinRT 的 IAsync 更新了一下之前的用法

**C++ coroutines: Short-circuiting suspension, part 1** https://devblogs.microsoft.com/oldnewthing/20191213-00/?p=103210

- 系列第五篇
- 增加的内容是 await_suspend 可以返回 bool，如果是 true 则照旧走 suspend 的路子，如果返回 false 则会立即执行 resumption 的逻辑
- 好处是可以减少 co_await 的嵌套，减少 coroutine frame

**C++ coroutines: Short-circuiting suspension, part 2** https://devblogs.microsoft.com/oldnewthing/20191216-00/?p=103217

- 系列第六篇
- 这篇主讲 `await_ready()` 的作用，返回 true 时代表状态已经执行完毕，没必要再创建 coroutine frame 然后立马再 reload back，所以这个时候编译器生成的代码会跳过这些，自动执行 resumption code

**C++ coroutines: no callable ‘await_resume’ function found for type** https://devblogs.microsoft.com/oldnewthing/20191217-00/?p=103219

- 系列第七篇
- 算是番外插曲，解释一下

    ```
    no callable ‘await_resume’ (or ‘await_ready’ or ‘await_suspend’) function found for type ‘Expression’
    ```

    这个错误


**C++ Weekly - Ep 271 - string.clear() vs string = ""** https://www.youtube.com/watch?v=3X9qK7HWxjk

- clear() 性能更好，语义也更明确
- 视频拿了 libstdc++ 的实现来说，`= ""` 内部真的是走了一次 assignment 有N多的内部检查，而 clear() 实际上就把长度给设置成了0

**Passing an enum by parameter** https://belaycpp.com/2021/05/03/passing-an-enum-by-parameter/

- 作者的意见是如果 enum flags/modes （数量要少于5个，最好就2-3个），作为模板参数要比作为函数参数传递更好，生成的代码也更好
- 实际上我觉得有点难评…主要使用场景有点受限，作为模板参数那就必须的得是 compile-time evaluated value，然后还有成员限制，带来的优势其实并不明显。
- 这里我觉得还是以更明确的语义触发比较合理

\#2

抽了点时间看了一下 https://github.com/franktea/coro_epoll_kqueue，即把 epoll/socket 操作封装成 coroutine。

这个 sample 太 demo 了，不过原理性阐述的部分还行。

给 send/recv/accpet 这些操作提供一个基础的 AsyncSysCall 基类包装了 awaiter，await_suspend() 中尝试一次 syscall 调用，如果返回 EAGAIN 说明继续操作会被阻塞，就 suspend 当前 coroutine 并且将 coroutine handle 存到对应的 Send/Recv/Accept 对象中；后续 resume 发生在 epoll_wait 返回某些操作可读/可写后继续

Task<> 抽象设计用于从 coroutine 中返回数据

一些嵌套 coroutine handle 的处理没细看，但是整体上的概念有了。

\#3

这周事儿太多了，所以没有继续写线性代数剩下的习题...

下周继续加油吧

---

好了这周就这样，下周见
