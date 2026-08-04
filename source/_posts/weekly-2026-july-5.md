---
title: 一周杂记 in Week 5 July 2026
categories: CODE-LIFE
date: 2026-08-04 23:14:12
tags: [杂记]
---
本周（07/27 ~ 08/02）是7月份最后一周，炎炎夏日啊。

## Life

\#1

周一中午吃着饭呢，人保给我打电话说周日的剐蹭对方车的定损出来了，800多，直接走了交强险。

感觉有点可以啊，这样一来的话来年保费都不会长太多

现在看，当时走保险确实是对的路子。

\#2

这周杭州温度直接飙红，周四直接冲上40℃，所以直接选择了居家办公，连同周五的日常居家，所以这周两天的 WFH。

看了一下佳明的记录，两天 WFH 压力值都下来了 🤣

周五 suyang 回杭州，中午还一起吃了个阿郭以及 afterglow 喝了杯咖啡。

\#3

这周一个很棘手的事是，秋宝周三开始发烧。

因为是人生第一次发烧，秋宝非常不适应，状态也不是很好，赶紧买了美林备用。

前前后后烧了三天，直到周五晚上才平稳下来；期间最高烧到39℃，几次都是靠美林迅速把体温压下来。这几天每天最关心的就是秋宝的体温下来没 🤣

但是最终还是要靠多补液，水或者牛奶，让免疫系统去把病毒杀死才行。

到了周末的时候烧基本退了，但是发现秋宝开始全身长疹子；媳妇儿说这是病毒感染正常的现象，但是疹子也需要几天才能消退。

因为连续好几天都处于生病状态，秋宝差不多时刻处于不安情绪中，化身挂件模式，一定要有人配在边上或者抱着她，不然就会大哭；晚上睡觉连自己床都不睡了 🤡

得等下周她恢复了再看看身体状态调整的事情了。

媳妇儿这周照顾秋宝也累的够呛，周日傍晚说啥都要开车出去转转 👿

\#4

这周抽时间把 Daredevil: Born Again S2 看完了

- **Daredevil: Born Again Season 2** 4/5 整体水准差不多回到当年 netflix s1/s3 的水准线，动作戏在线，但是个别剧情感觉有点可以。不过看预告之前的 defenders 都被马律师给抢救回来了啊，那能不能把 Colleen Wing 也给抢救一下

## Work

\#1

**CppCon 2023 | Iteration Revisited: A Safer Iteration Model for Cpp - Tristan Brindle** https://www.youtube.com/watch?v=nDyjCMnTu7o&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=52

- raw loops with iterators 容易出问题，并且 iterators 是 unsafe by default
- 标准的做法是 ranges，作者的做法是他的 https://github.com/tcbrindle/flux；有点类似 ranges，但是不用 `|` 而是用 `.` 而且也不追求 ranges 这种统一性
- flux 外部交互的接口都做了 bounce checking，目前现代架构 bounce checking 的代价其实很小了，并且也提供了 accessories with unchecked

**CppCon 2023 | Applicative: The Forgotten C++ Functional Pattern - Ben Deane** https://www.youtube.com/watch?v=KDn28TZdKb4&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=54

- 科普了 monoid / monad / applicative，然后讲如何改进 optional / expected 体统的 monadic operations，因为作者觉得那几个还是非常不直观不好用
- 虽然我觉得作者改的 fmap + lazycall 反而更是花架子。。。。

**C++ Weekly - Ep 543 - The constexpr Evolution of std::array** https://www.youtube.com/watch?v=T-d8l3s4Nx8

- std::array 对于 constexpr 支持的演进

**C++ Weekly - Ep 108 - Understanding emplace_back** https://www.youtube.com/watch?v=uwv1uvi1OTU

- 这个比较基础了，介绍 emplace_back，construction in place

**C++ Weekly - Ep 107 - The Power of =delete** https://www.youtube.com/watch?v=aAvjUU0m6AU

- 利用 `= delete` 把某个重载给标记，但是如果遇到 implicit conversion 的情况，可能不如 SFINAE 好用

**C++ Weekly - Ep 106 - Disabling Move From const** https://www.youtube.com/watch?v=nP3TnWBonlY

- 利用 `=delete` 把 const T&& 的 move ctor 和 move assign 关闭，强制报错 move from const
- 不过一堆 static analysis tools 应该可以直接 report 这个问题

**C++ Weekly - Ep 105 - Learning "Modern" C++ - 5: Looping and Algorithms** https://www.youtube.com/watch?v=A0-x-Djey-Q

- using std algorithms to replace raw loops

**C++ Weekly - Ep 104 - Learning "Modern" C++ - 4 : const and constexpr** https://www.youtube.com/watch?v=UYEyHlynkPc

- using const / constexpr and clang-tidy

**C++ Weekly - Ep 103 - Learning "Modern" C++ - 3: Inheritance** https://www.youtube.com/watch?v=43qyUASBeUc

- 用虚函数的一些 tips，都应该能倒背了。。。

\#2

这周主线两条路，第一条是清理 asan 集成的一些善后的事情，问题比较小。

另外一条是做我们开发环境从 almalinux-8 迁移到 almalinux-9，因为之前 alma8 的 ansible playbook 写的比较乱，而且是从老 repo 拆出来的，有很多冗余，所以这次打算好好整理一下，给全新的 alma9 的 roles 里搞一套新的 playbook。

因为有 codex 帮助，整体做的还是比较快的，就是 token 烧的确实有点快。

AI 现在写的 ansible tasks 既标准又支持幂等，只要你提前把路径规划好，具体实现人家出来绝对顶得上专业写 ansible 的专家

比较踩坑的反而是 alma9 vagrant box 升级后为了朝 cloud image 靠拢，很多安全措施都比之前严格，比如关闭 SELinux 需要更改内核配置还要重启...

cloud-init 默认锁 vagrant 用户密码这个有点离谱了，还好最后 AI 找了一个 workaround，而且不依赖 ansible，不过需要 mkisofs。

另外一个坑就是现在每次 vm destroy & create 之后宿主机 ssh 连接会提示因为 identification change 给 block 了，因为新镜像每次的 fingerpint 都是新的。

标准做法是每次 destroy vm 之后清理一下宿主机的 ssh known_hosts 文件，但是其实大家更喜欢的做法是往 ssh config 的目标 host alias 加入

```
StrictHostKeyChecking no
UserKnownHostsFile NUL       # /dev/null on macos/linux
```

Alma9 搞完之后下周差不多就要开始搞 ubi-9 了，产线部署的镜像都得换这个，而且运行时镜像强制用 ubi-9 micro

下周再不弄要来不及8月份 release 了 🤣

---

这周就这样，下周见
