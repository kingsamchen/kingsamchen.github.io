---
title: 一周杂记 in Week 4 June 2026
categories: CODE-LIFE
date: 2026-06-28 22:50:41
tags: [杂记]
---
本周（6.22 ~ 6.28）是六月份第四周，下周三就是7月1号了。

## Life

\#1

这周媳妇儿尝试给秋宝使用重力球学饮杯，斥巨资80买了贝亲的杯子，但是最终以失败告终。

我已经发现秋宝完美继承了媳妇儿不喜欢脱离舒适区以及不喜欢努力的毛病

于是就过了一天，重力球吸管和学饮杯都收起来了 🤡

\#2

周日中午和媳妇儿去了银泰的一家意大利餐厅，价格还是挺贵的，2人吃了350多，优惠前500多

但是呢这家餐厅的味道实在太一般了，基本是属于那种网红打卡餐厅

最离谱的是麻辣蜗牛的酱汁是四川红油...这尼玛一点都不搭啊，280多一块的西冷牛排肉质也一般。

反正以后肯定不会去了

吃完之后去了一趟联想店，把老婆看上的小新16给订上了，不过因为是国补，所以没有现货，说是要第二天才能到店里取。

\#3

周日下午开车和 Hogan 到亲橙里上了一节沙袋课

全场加起来一个半小时，算上热身，但是打沙袋是真的累多了。

而且我发现我脚跳踢是真的不太行，后面看看有没有机会专门加强一下。

单纯拳击，不管是直拳还是勾拳摆拳感觉距离感还是挺好的，而且收回也比较及时，打起来还是有一些感觉的；就是肘击练的皮都擦伤了 🤔


## Work

\#1

**CppNow 2025 | Making A Program Faster - Multithreading & Automatic Compiler Vectorization - Ivica Bogosavljevic** https://www.youtube.com/watch?v=GTAE_znTvuk&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=39

- 前半部分讲 openmp ，后半部分讲 vectorization
- 前半部分不评价，离太远，后半部分感觉可以等等 std::simd 或者类似的库？

**C++ Weekly - Ep 538 - Finding Memory Bugs at Compile time** https://www.youtube.com/watch?v=FZaoT7Kx_L0

- 最新的 gcc trunk 已经可以把 asan 的错误直接在编译期暴露出来了

**C++ Weekly - Ep 126 - Lambdas With Destructors** https://www.youtube.com/watch?v=9L9uSHrJA08

- lambda 本身不能手写析构函数，但 lambda 的 capture 会变成 closure object 的成员；如果 capture 进去的对象有析构函数，那么这个 lambda 对象销毁时，也会自动调用被捕获对象的析构函数；不过其实也比较符合直觉

    ```cpp
    #include <iostream>

    int main() {
        auto l = [
            guard = [] {
                struct Guard {
                    int a;
                    int b;

                    ~Guard() {
                        std::cout << "destructor: a = " << a
                                  << ", b = " << b << '\n';
                    }
                };

                return Guard{1, 2};
            }()
        ] {
            std::cout << "lambda body\n";
        };

        l();

        std::cout << "leaving scope\n";
    }
    ```


**C++ Weekly - Ep 125 - The Optimal Way To Return From A Function** https://www.youtube.com/watch?v=9mWWNYRHAIQ

- 虽然编译器优化增加了不确定性，但是最好的方式就是返回返回类型需要的值，核心还是为了能触发 RVO

**C++ Weekly - Ep 124 - ABM and BMI Instruction Sets** https://www.youtube.com/watch?v=i0wrcw8W75w

- intel/amd 都推出了高级的 bit manipulation instructions，一些常见的 bitwise ops 现在会被编译成对应的指令

**C++ Weekly - Ep 123 - Using in_place_t** https://www.youtube.com/watch?v=Fs2nhjIysKA

- optional / variant / any 这些如果构造的时候预期是调用 T 的构造函数，推荐使用 inplace_t 的重载，性能更好

\#2

这周 scheduler 新版本正式推到产线了，我一边围观看看有没有什么预期外的问题，毕竟这是换 CMake + Conan 之后的第一个版本。

还好观察了一周也没有出现奇怪的问题，所以给 scheduler 做的构建升级算是顺利地走出了第一步了。

和 chao 简单讨论了一下，新一期的 release 定了大概三个目标：

1. 调整 gitlab 的 pipeline，然后正式删掉废弃的 Makefile 相关文件，并且清理掉 vagrant 中冗余的 tasks
2. 引入 sanitizers；这里有个风险点是这一期 jenkins 不一定直接启用，要看有多少模块开启后需要 fix
3. 调研 ubi-9 和 alma-9；这一步是为后续工具链升级做提前准备，而且 Ops 也提了公司层面要求了 ubi-8 的支持期快到了

\#3

周五晚上研究了一晚上差不多搞明白 gitlab-ci 怎么跑的了，并且验证通过了 install conan packages。

后面可以把 scheduler 的 gitlab-ci 给重新搞一下，然后正式删掉 Makefile 相关的文件了

周三开始 cursor 不能用了之后感觉做事情效率都变低了 🤡

---

这周就这样，下周见
