---
title: 一周杂记 in Week 1 Oct 2026
categories: CODE-LIFE
date: 2026-10-05 23:44:02
tags: [杂记]
---
本周（9.28 ~ 10.4）是10月份第一周，周四正式进入十一假期。

## Life

\#1

这周周一到周三（9.28 ~ 9.30）上了三天班，但是这几天感觉一点都不是很轻松。

估计也和把旧的 apache/httpd 的 server 迁移到新的上还有一堆活还没做有关。

不过周三在家办公的时候感觉整体倒还好，可能也和马上放假了心态会平和一点有关。

不过到了晚上因为公司的 codex 账户还有二十几美刀的额度，所以想趁着最后一天跑个大的，所以这天晚上睡的有点晚，一直到凌晨两点多开了一个千行多的迁移任务之后才去睡觉。

这里吐槽一下 OpenAI 的 GPT-6.1 Sol Max，实在太太太慢了，开了 fast mode 还是很慢；蔡司令说坊间传闻这个其实就是 6 Astra 换了个马甲，我觉得还真有可能。

因为实在太慢了我后面直接换成了 GPT-6 Sol Max，不然怕是一晚上都不用睡觉了。

早上快十点起床看了一下，不错，迁移的 task 都跑完了，还帮我把现有的 tests 都跑通了。

\#2

10.1 号因为起床比较晚，所以早上属于休息为主。

下午3点和包子约了拳击训练营，打算在回老家前上一次课

这次练的还是很爽的，直拳勾拳组合进一步熟练。

\#3

10.2 号开车到西站，带着老婆秋宝和丈母娘回苍南

西站确实不错，虽然路程比东站远，但是上高架之后路非常通畅反而速度更快

而且因为我们买的商务座，所以可以在商务座专门的候车室休息；候车室人少，毕竟买商务座的人确实太少，而且这个也是我们的目的，减少病毒接触。

候车室还有零食和茶水，卫生间也非常干净，快开始检票时工作人员还会带领提前过闸机进车厢，体验拉满。

整体来说商务座的体验确实是非常舒适，所以就看这溢价值不值得。

西站还有一个好处，一天停车费封顶18块钱，5天四舍五入等于不要钱。

10.3 号下午丈母娘开车带着我和媳妇儿跑到附近平阳县的坡南转了一下

那边有个古街商业街，体感上和杭州的河坊街小河直街都差不多。

现在也算各地古商业街模式一大抄了。

傍晚开到一个农家乐吃饭，路上稍微堵了一会儿；农家乐的东西味道确实不错，就是牛筋做法不是很合我口味

## Work

\#1

**CppNow 2025 | Coinductive Types in C++ Senders - Building Streams out of Hot Air - Steve Downey** https://www.youtube.com/watch?v=POXB5xRai74&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=27

- 大部分都是 FP 那种构建一些最基本 constructs 的内容，比如 just / either 这些
- 算是从 FP 角度来阐释 S&R 的最基本设计
- 虽然和 S&R 几乎没啥关系

**CppNow 2025 | Growing Your Toolkit From Refactoring to Automated Migrations - Matt Kulukundis** https://www.youtube.com/watch?v=vqFEKvI0GmU&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=26

- 作者分享在 Google 的时候如何做超大规模项目的迁移工作
- 核心就是 single-step 到 multiple steps 让repo每次前进都再一个接受的范围内
- 然后很多底层的工具其实非常有帮助，等等；还提到他们升级一个库的6步骤，引入 shim layer 做兼容层的想法非常重要
- 不过现在有AI了，感觉很多重构操作都能变的更加高效

**CppCon 2023 | Lightning Talk: Making Friends With CUDA Programmers (please constexpr all the things) Vasu Agrawal** https://www.youtube.com/watch?v=TRQWxkRdPUI&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=66

- CUDA 的函数需要标记 `__device__` 指明在 cuda kernel 里运行，这样 nvcc 才能为其编译为 gpu 执行函数
- 但是编译的时候开启 `--expt-relaxed-constexpr` 之后 constexpr 函数不需要标记就能直接在 cuda kernel 里运行，这样一堆现成的库函数和三方库都可以直接在 cuda 里运行
- 所以作者倡议：constexpr is good, constexpr all the things

\#2

这周在老家抽了点时间同步了几个 fawkes 的变更到自己的仓库

[request should carry connection endpoints](https://github.com/kingsamchen/fawkes/pull/36)

[Add a type-erased request context](https://github.com/kingsamchen/fawkes/pull/37)

[Add custom not-found handler support for fawkes](https://github.com/kingsamchen/fawkes/pull/38)

[Server handles request body too large](https://github.com/kingsamchen/fawkes/pull/39)

\#3

之前给 fawkes github 搭的 CI 没有设置 sanitizers options，所以实际上检测范围并没有到很大的程度

所以这次专门给 fawkes 加上了

[Set ASAN/LSAN options for Github CI tests](https://github.com/kingsamchen/fawkes/pull/40)

其实核心就是加上

```bash
ASAN_OPTIONS: "detect_leaks=1:detect_stack_use_after_return=1:malloc_context_size=15:print_suppressions=0"
LSAN_OPTIONS: "exitcode=23:report_objects=1:print_suppressions=0"
```

这俩环境变量

不过实际上我觉得应该再加上 ubsanitizer 的 options，不过后面再搞好了

---

这周就这样，下周见~
