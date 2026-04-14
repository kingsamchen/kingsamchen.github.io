---
title: 一周杂记 in Week 2 Apr 2026
categories: CODE-LIFE
date: 2026-04-13 16:24:10
tags: [杂记]
---
本周（04/07 ~ 04/12）时四月第二周，这周因为公司搬家原因所以一周都居家办公。

## Life

\#1

这周继续在家办公，不过因为开发机恢复了，所以整体体验还行。

就是家里秋宝确实容易干扰效率 🤡

下周二就要回新办公室了，还不知道新办公室怎么样。

另外，因为新办公室楼下就有一家 manner，所以下单买了一个乐扣的咖啡杯，以后和同事直接下楼买 Manner，自带杯-5元，每四次就省出一杯了 🤡

\#2

东大政府开始围剿机场了，狗子云节点大面积不可用，YouTube学习视频都没法看，notion也没法使用...

在前同事群吐槽的时候马总说我以前用的那个梯子还挺稳的，于是我赶紧用以前的推介费搞了一年的 Lite

虽然是 Lite 但是可用的节点也能轻松 4K youtube，每月100G流量，爱了爱了。

另：周日时候狗子云更新了节点，新一批节点似乎又可以比较正常使用了 🤷‍♂️

\#3

主卧的马桶又漏水了，叫了科勒的维修师傅上门，第一次没找到真正的 root cause，以为是一个开关松了导致链条容易打结。

“修好”当晚有立马复现了...

现在只能约时间再上门检修 🫠

## Work

\#1

**CppNow 2025 | Streamlining C++ Code - Avoiding Unnecessary C++ Object Creation** https://www.youtube.com/watch?v=PYZx6jrlm4M&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=46

- 前半段比较简单，但是后半段干货还是非常多的，尤其是 pair/tuple/optional/variant/expected 这些大家平时用的时候没有那么注意，in-place construction 写起来也相对繁琐一些，所以都不怎么在意
- 另外 associative conatiner transparent lookup 这个就是更容易被忽略

**CppNow 2025 | How to Pinpoint Runtime Errors in Mixed C++ & Rust Code For Busy Engineers - Steve Barriault** https://www.youtube.com/watch?v=ifqIdCvkmuU&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=44

- 推销他们家通过分析源代码结合 formal methods/verifications 来定位潜在的 runtime errors 的产品
- 并且保证不会有 false negative

**C++ Weekly - Ep 527 - A clang-tidy Check You NEED To Use! (Designated Initializers)** https://www.youtube.com/watch?v=WROn9H5FLoY&t=10s

- 省流：用 clang-tidy 的 modernize use designated initializer

**C++ Weekly - Ep 526 - Stop asserting on nullptr!** https://www.youtube.com/watch?v=vivaDVFNj4U

- Jason 认为这个做法很不靠谱，正确的方案是通过调整接口语义来处理，例如 pass by reference / value

**C++ Weekly - Ep 162 - Recursive Lambdas** https://www.youtube.com/watch?v=M_Mrk1xHMoo

- 我觉得是整活….
- 其实可以用 std::function<> 接一下的；另外这视频比较老了，C++23里面可以用 deducing this

**C++ Weekly - Ep 161 - The C++ Box Project** https://www.youtube.com/watch?v=imuINhOrjFw

- Jason 自己搞的一个学习型项目，一个比较有反馈的 cpp dev environment

**c++异步：asio的scheduler实现** https://mp.weixin.qq.com/s/pLGV1Kyba-joBHZec4YZyA

- 基于 1.16 版本的简单讲解，但是讲道理我觉得这个源码分析做的挺一般的，看完并不能有太多的收获，甚至对于 strand 的分析不如之前网上看到的一篇比较粗显得分析中的 diagram 来的直观

**Care is needed to use C++ std::optional with non-trivial objects** https://lemire.me/blog/2023/01/12/care-is-needed-to-use-c-stdoptional-with-non-trivial-objects/

- `std::optional` 好用但是对于 non-trivial types，要注意避免多余的拷贝
- 而且文章里示例还漏了一点，如果用构造函数，一定要用 `std::inplace` 的版本，避免临时拷贝+多余的移动，这个很多人不会注意
- 刚好最开头有个 **CppNow 2025 | Streamlining C++ Code - Avoiding Unnecessary C++ Object Creation** 的分享要多看看

\#2

抽了个时间把 fawkes 的几处用 `std::function<>` 的地方换成了 `std::move_only_function<>`

遇到几个问题，这里简单列一下

第一个是 std::function<> 有一个非常奇葩的特性，他的 operator() 是 const member function，但是他可以 store mutable lambda

而 std::move_only_function<> 支持所有的 qualifier，可以反映到它的 operator() qualifier 上，但是需要显示列出，这导致如果标记了 const qualifier，就不能 store mutable lambda

```cpp
#include <functional>
#include <utility>

int main()
{
    int i = 0;
    auto l = [i]() mutable -> int {
        return ++i;
    };

    std::function<int()> f = l;
    auto a = std::as_const(f)();

    std::move_only_function<int()> mf = l;
    auto b = mf();
    // auto c = std::as_const(mf)();
    //  <- note: candidate 1: '_Res std::move_only_function<_Res(_ArgTypes ...) noexcept (_Noex)>::operator()(_ArgTypes ...) [with _Res = int; _ArgTypes = {}; bool _Noex = false]' (near match)
}
```

这个不一致地方导致使用后者的话，如果 middleware 的 handler 是 non-const，就会需要一些 trick 来完成。

于是我略微思考了一下，middleware handler 如果修改自身状态，本身也是非常危险的，因为同一个 middleware 会被一个或者多个 route handler 并发访问到，如果不是 immutable 就需要额外的 synchronization，而这又会带来一些其他问题。

所以想了一下，我把 middleware 改成了默认情况下必须要是 immutable；如果真有 mutable state 需求，可以内部自己用 `mutable` member 开洞，本身这种做法也是非常不推荐的。

另一个问题是 std::move_only_function<> 的 move ctor 是 noexcept 的，它的 move assignment 不是，标准说这是为了后续支持 allocator 做的妥协

这个 PR 见 https://github.com/kingsamchen/fawkes/pull/27

\#3

这周帮 scheduler conanization 进度暂停，因为要优先做一个 scheduler ai bot

做了差不多一半发现卡在一个 design 上，和 xdm 讨论一下之后决定先做一个 trick 先，等后面 demo 之后如果真的要产品化落地再把这个点重新 refine / carefully design 一下

结果接着做了一会儿发现又卡在了另外一个点上，于是暂停一下等 Roro 那边给一个 workaround

下周看看能不能把这部分 demo 搞完

---

好了这周就这样，下周见
