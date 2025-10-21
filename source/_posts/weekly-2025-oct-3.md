---
title: 一周杂记 in Week 3 Oct 2025
categories: CODE-LIFE
date: 2025-10-20 22:51:20
tags: [杂记]
---
本周（10/13 ~ 10/19）是十月份第三周，这周太忙了，而且周末开始降温了。

## Life

\#1

周四下午请了半天假，中午刚下班就开车到媳妇儿家，把东西一起搬上车，最后吃了饭之后带着娃开着车回到了拱墅的家里。

花了一天的时间把东西 setup 之后，就算正式开启了娃在城西的家的生活了。

前两三天是磨合期，丈母娘也睡在我家里，不过因为我妈也在，实在没房间了，所以丈母娘睡了两天的沙发。

我自己被赶到了小次卧，只有一张宽不到1m的床 🤣

不过为了秋总，也只能这样了。

\#2

周四下午把娃接到拱墅这边之后，下午我看还有时间，就到医院把奶黄包提前一天接出院。

这小咪恢复良好，就是在医院太孤单了，所以有点担心他太难适应，所以见恢复得差不多就赶紧带出院了。

放归时候一溜烟跑得飞快，后续几天碰到他估计对我还怀恨在心，老远就赶紧跑

\#3

这周六开始降温了，而且是从接近30度的气温直接降到20度，半夜躺在床上窗户不管紧都能给冻醒

看了一下天气预报，未来十几二十天都是这个气温，所以应该是真的进入秋天了吧。

\#4

- **同甘共苦 4/5** 结局有点意思...前面以为是 The Thing 那种片子，没想到是对于伴侣关系的探讨的…

## Work

\#1

**CppCon 2023 | Back to Basics: Iterators in C++ - Nicolai Josuttis** https://www.youtube.com/watch?v=26aW6aBVpk0&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=6

- 这期 talk 质量好
- 另外提到了 C++ 20 引入的 ranges 的 views::filter 会 cache begin iterator，这是一个大坑。但是因为标准说了这是 feature 而且如果修改了 filter iterator 指向的值并且结果不满足 filter predicate，就是UB，所以只能自己用的时候注意，下周抽个时间专门写个 post 记录一下吧

**Bit-vector manipulations in standard C++** https://quuxplusone.github.io/blog/2022/11/05/bit-vectors/

- 讲解各种 bit string 容器/设施中实现找到第一个不为0的 bit 的方案对比
- libc++ 的 find 对 vector<bool> 做了 SIMD 优化，其他家没有

\#2

周五周六连续两个晚上头脑风暴了一下，把 fawkes middleware PoC 弄完了，下周就是找个时间把代码用比较正式的方式写一遍，finalize 下来。

\#3

周末再写上面那个 middleware PoC 的时候，用了一把 templated lambda 来做 for_each tuple，但是遇到了下面这个问题

简化一下就是

```cpp

void foo(int n) {
    [](int n){
        (void)n;
    }(n);
}

int main() {

}
```

在最近几个版本的 gcc 下都有编译告警

```
<source>: In lambda function:
<source>:3:12: error: declaration of 'int n' shadows a parameter [-Werror=shadow]
    3 |     [](int n){
      |        ~~~~^
<source>:2:14: note: shadowed declaration is here
    2 | void foo(int n) {
      |          ~~~~^
cc1plus: all warnings being treated as errors
Compiler returned: 1
```

clang 没有问题，见 https://t.co/tBCXWq5mW3

讲道理，gcc 的告警在我看来是没有道理的，毕竟都不是一个 scope

后面有空的话专门写一个 post 记录一下 tuple 的 for each 好了

---

这周就这样，下周见
