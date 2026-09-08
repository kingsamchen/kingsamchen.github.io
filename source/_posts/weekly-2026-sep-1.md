---
title: 一周杂记 in Week 1 Sep 2026
categories: CODE-LIFE
date: 2026-09-06 23:28:35
tags: [杂记]
---
本周（8.31 ~ 9.6）是9月份第一周。

这周开始搞 httpd -> fawkes 的迁移了，有点忙，事情也有点多。

## Life

\#1

这周秋宝运动能力进步神速，已经能维持走路状态接近一分钟了

虽然走起来的时候还不是特别的平衡稳定，但是已经非常不错了，就是姿势有点像鸭子，比较搞笑。

我估摸着再过2月这娃的性格可能都开始想跑步了

\#2

这周 Ricardo 回国了，老同事聚一聚；而且 Suyang 也回到办公室继续在上一周班。

所以这周吃饭比较多

Jed 周三晚请吃肉本家，算是给 suyang 践行；周四晚上我还额外拉上了 scheduler 能来的兄弟们，一起吃了一顿老邻舍，给 Ricardo 接风洗尘，也预祝 suyang transfer 顺利

不过吃太多的一个副作用是，晚上容易吃撑导致消化不良睡眠不好。

于是这周三四两天睡眠不太好，四五六三天连续 HRV 下跌 🤡

\#3

这周六下午上了拳击训练营

依然30分钟热身，一个半小时的学习+训练

1VS1 确实体验更好，这次也打爆爽

这次继续矫正了不少勾拳的姿势

## Work

\#1

**Boost String Algorithms Library** https://www.youtube.com/watch?v=23eXt2EuMLM&list=PLJDO7P5jAoXznYam4ucdMMcKJky8UHbhC&index=2

- 介绍 boost string algorithms，用具体实际例子来说明
- 但是我觉得这个（子）库太老了，已经十几年没更新了，还在坚守 03 标准，接口也是老化的不行，还有 locale 是接口一部分这个蛋疼的设定
- 所以我个人实际场合不会太考虑这个库，除非项目很老且用了这个

**CppCon 2023 | C++ Object Lifetime: From Start to Finish - Thamara Andrade** https://www.youtube.com/watch?v=XN__qATWExc&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=60

- 临时对象的生命周期 + reference lifetime extension
- 强烈推荐

**CppCon 2023 | Why Loops End in Cpp - Lisa Lippincott** https://www.youtube.com/watch?v=gyD1AJ8I5NE&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=61

- 看了十几分钟直接让 GPT 给我 summarize 了
- 这个大姐一如既往的 formal verification 的特点
- GPT 总结，其实就一句：A loop terminates because every iteration makes progress according to some measure that cannot make progress forever

\#2

基于 fawkes 的 scheduler-api server 的骨架这周写完了，外面那个把 fawkes/server + primary io_context + signal handling .etc 糊起来的东西光名字就想了老半天，最后索性直接用 class App

最大的坑应该是 httpd 的 route paths 迁移有了，fawkes 原生的 route spec 因为采用了 go/httprouter 的做法，对于 ambiguity 会有冲突。

一开始想着的是做一些小调整 + ngx url rewrites workaround 掉的，但是做着做着发现这个方案不太可行。

最后重新写了一个 legacy route tree，先支持所有现有的 route paths，能迁过来再说，反正后面还要做 v2 的规划

这个 legacy route tree 的实现又一大半是 AI 写的，几轮调整后基本达到了我的审美要求

\#3

这周在给 new scheduler-api server 写一个 `runOnExecutor()` 把之前一个惯用法给回顾了一下，现在 finalize

场景：函数参数通过 `template<typename F>(F&& f)` forward 传入一个 function object，但是内部希望保存一份

- 右值的话走 `std::move` 避免拷贝
- 左值的话只能 copy 一份

标准做法是

```cpp
std::decay_t<F> fn = std::forward<F>(f);
```

不过如果能利用 auto 的推导，则可以直接

```cpp
auto fn = std::forward<F>(f);
```

因为 auto 值推导会自动做 decay

---

这周就这样，下周见
