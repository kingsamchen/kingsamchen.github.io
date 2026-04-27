---
title: 一周杂记 in Week 4 Apr 2026
categories: CODE-LIFE
date: 2026-04-27 17:02:53
tags: [杂记]
---
本周（4/20 ~ 4/26）是四月份第四周，这周大部分时候继续在家办公。

## Life

\#1

这周钦定了可以在家办公，所以除了周一之外其余几天都居家了。

周一因为美国那边几个和金融审计相关的 CxO 来杭州，所以搞了一个新办公室的乔迁活动，几乎全员都在了。

不过周一味道还是挺大的，就是不知道两三周这味道怎么散的完，估计过完一整个夏天都不一定能挥发干净，尤其夏天到了还要开冷气，抑制了挥发。

周五中午约 suyang 一起吃了一顿清风，然后又去了一个新的咖啡馆喝了杯冷翠的美式，感觉味道还可以。

\#2

然后讲点悲惨的吧

周五下班后去了乐刻锻炼了一个多小时，30分钟5:55配速5公里的跑步机加上器械负重训练，结果晚上到家后没有吃碳水，跟老婆吃了毛豆/土豆的减脂餐，导致皮质醇一直在高位，晚上死活睡不着，直接失眠，吃了两粒褪黑素都没用，一直清醒到快凌晨四点才睡着。

中间忘了凌晨几点，想到这个非常生气拿起手机就往地上咋，结果直接把手机屏幕砸坏了，1/3的屏幕区域直接裂痕，而且整块屏幕大多数时候显示都是绿色，几乎没法使用。

周六上午8点多又被老婆微信吵醒，问我要不要去医院复查个血脂，我直接无语。

不过手机没救了只能买个新的了，看来看去JD上买了个 iPhone 17 256GB，JD 可以一小时达

到了之后开始研究怎么把老手机数据迁移过去，折腾了一下午才借助 Apple Devices 这个 Apple 开发的 iTunes 替代品把老手机数据转移过去。

老13才用了4年，虽然 faceid 被摔坏了，但是电池健康度还有87%，感觉还能再战1-2年，结果这下直接换了个新的，导致4月份的支出直接把预算打爆了，感觉亏的有点多啊。

## Work

\#1

**CppCon 2023 | Symbolic Calculus for High-performance Computing From Scratch Using C++23 - Vincent Reverdy** https://www.youtube.com/watch?v=lPfA4SFojao&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=34

- 有点牛逼，基本看不懂
- 用纯 C++23 实现一个轻量的、编译期的 symbolic calculus 系统，让数学公式可以在 C++ 里以接近自然数学表达式的方式书写、组合、求导、积分，并且最终仍然生成接近手写高性能数值代码的机器码。
- 而且一个牛逼的点是公示系统是 stateless，信息都编码进类型系统了，只有在运行时的时候才会附上运行时信息

**C++ Weekly - Ep 156 - A C++ Conference Near You** https://www.youtube.com/watch?v=9ENXVGFk0s4

- 科普一些比较有规模的 cpp conferences

**C++ Weekly - Ep 155 - Misuse of pure Function Attribute** https://www.youtube.com/watch?v=FR5G_miCHtE

- `[[gnu::pure]]` 的效果以及不正确使用的严重副作用

**C++ Weekly - Ep 154 - One Simple Trick For Reducing Code Bloat** https://www.youtube.com/watch?v=D8eCPl2zit4

- 如非必要，不要定义 non-trivial 的 destructor，尤其是那些 empty body 的 dtor，会导致类型变成 non-trivial 然后编译器会生成很多不必要的指令

**C++ Weekly - Ep 153 - 24-Core C++ Builds Using Spare Computers!** https://www.youtube.com/watch?v=JRmL0g87cc0

- 闲置机器变成构建机，ccache + distcc/icecream 来缓存分发任务
- 有条件的可以参考一下

**C++ Weekly - Ep 152 - Lambdas: The Key To Understanding C++** https://www.youtube.com/watch?v=CjExHyCVRYg

- 从 C++ 11 ~ 20 根据要点列了一些 lambda 的学习点，可以按图索骥

\#2

这周抽了 1~2 天时间，终于把帮 chao 做的 scheduler ai-bot 那部分给完成了，并且和 Guts 赶着周五 all hands 的 deadline 之前做了 demo 的录制，起码不会烂尾了，可以长舒一口气

周五 all hands 的时候作为压轴的 demo presentation 过了一遍（其实是因为交的太晚了所以自然就在最后了），虽然不知道大家有没有在听，但是好歹被致谢了一下，也还可以啦。

考虑到这个 demo 应该不会那么快计划落地成具体的 scheduler product feature，所以后面应该有时间和精力可以继续给 chao 做 build arch 的改进了

总感觉下周可以敢在5.1前，趁着 cursor 还能用，先把 scheduler 的所有模块在 local vm 里先迁移到 CMake + conan 上

---

好了，这周就这样，下周见
