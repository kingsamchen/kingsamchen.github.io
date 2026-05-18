---
title: 一周杂记 in Week 2 May 2026
categories: CODE-LIFE
date: 2026-05-17 23:08:54
tags: [杂记]
---
本周（05/11 ~ 05/17）是5月份第二周。

## Life

\#1

这周美国 scheduler 组有个同事来杭州，这哥们老家是广东的，而且是最近几年才拿到绿卡，之前还以为他是 ABC，因为他的中文实在太像 ABC 了 🤣 估计是从小说粤语影响的。

这周 Roro 也回国了，说是呆到 5.30 才去美国，我们都说他是不是快把老婆孩子也都一起带到美国去了，开始都研究好美国的幼儿园了。

这周一、周二、周三都有吃饭，不过不能每天晚上都太晚回家，所以周二晚上的饭局就没去了，早点下班带娃。

说起来 jiancheng 月底也要去美国了 🤔

\#2

这周三早上到办公室才听说我们服务又出了一个事故，还拉了 warroom。

稍微了解了一下发现有点坑，不过还好这个 incident 外包土狗全程没有参与，所以锅不怎么会飞到头上

没想到周六晚上 pagerduty 又叫了，打开手机一看 chao 说 db 挂了三个服务都有影响，而且又拉了 warroom 🤣

第二天看了一下群信息，说是周六下午往 db 导数据，但是 db 流量没抗住出现问题，但是告警有点鸡肋，没有及时通知，发现的时候已经雪崩了...

下周二开始就是我 oncall 了，我去别再搞出什么幺蛾子哦 🤡

## Work

\#1

**CppNow 2025 | Computing Correctness | Is your C++ Code Correct? - Nick Waddoups** https://www.youtube.com/watch?v=iRWyi09ftlY&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=43

- 形式化验证，比较适合算法类型的实现
- 后面那个 dafney 应该是他们搞得一个实验性的形式化验证的类 CPP 的语言

**C++ Weekly - Ep 532 - What Does var{} Do?** https://www.youtube.com/watch?v=A3Kr6Q4bIZs

- `T var{}` 会 default initialize / aggregate initialize / zero initialize 🤡 变量 ，避免 uninitialized access
- 另外 asan / ubsan 并不能 catch 这个问题，要用 memory sanitizer，并且为了避免大量的误报，有评论说是要整个 codebase 包括 libstdc++ 都要 compiled with msan…

**C++ Weekly - Ep 142 - Short Circuiting With Logical Operators** https://www.youtube.com/watch?v=WhOdvwRku9Y

- 其实我觉得是 short circuiting 要注意参与判断的值不要变成最终写回去的值，或者说参与判断的值实际上会变成一个常量

**C++ Weekly - Ep 141 - C++20's Designated Initializers And Lambdas** https://www.youtube.com/watch?v=L8Egig4qH0I

- designated initializer 里可以放 immediately invoked lambda…

**C++ Weekly - Ep 140 - Use `cout`, `cerr`, and `clog` Correctly** https://www.youtube.com/watch?v=lHGR_kH0PNA

- cout 写 stdout 有buffer
- cerr 写 stderr 无 buffer；clog 是 buffered cerr

**C++ Weekly - Ep 139 - References To Pointers** https://www.youtube.com/watch?v=0QOxC7ADT80

- 除了当 out param 好像其他地方就用的少了吧

\#2

这周把 scheduler tests 都跑通了，local vm 这部分 cmake/conan 化都完成了。

踩了一个 libscheduelr.so 静态链接的 openssl 和 httpd 自己加载的 openssl 符号冲突导致 httpd crash 进而 tests failure 的坑

一开始想的方案是，能不能让 opus 研究一下把 httpd 会导致加载 libssl 动态库的 mod 都停了，但是又突然想到，是不是有办法可以让 libscheduler.so 链接 openssl 的时候不要把符号导出，就像 Windows DLL 那样默认符号隐藏，但是又不用改源码。

还是 AI 比较给力，一通思考后说可以直接再链接的时候做手脚就行，于是直接通过修改对应模块的 CMakeLists.txt 来隐藏 libscheduler.so 链接 openssl 的符号，最后重新跑了一下测试，问题完美解决。

现在发现 AI 来处理这些玄学问题越来越好用了 🤡

---

好了这周就这样，下周见
