---
title: 一周杂记 in Week 3 May 2026
categories: CODE-LIFE
date: 2026-05-25 21:54:28
tags: [杂记]
---
本周（5.18 ~ 5.24）是5月份第三周，这周天气变化有点大了。

## Life

\#1

周三醒来的时候发现曼城居然平了伯恩茅斯，直接让阿森纳提前一轮夺冠。

作为兵工厂的低调球迷，看到球队时隔22年再次获得英超冠军，实在是高兴坏了。塔子哥牛逼！虽然现在进攻实在是有点难看，但是，硬度确实比以前要上来了。之前三连亚太伤了。

这样一来最后一轮可以做一些战术调整，全力备战月底的欧冠决赛了，不知道这次欧冠能否圆梦，拿下赛季双冠 🤔

\#2

这周五秋宝满一周岁，下班回家后特意挑了几张秋宝比较搞的照片组合了一下发朋友圈

对比了一下刚出生那会儿的样子，这一年的变化是真的大呀

不知道明年这个时候的秋宝是怎么样子的呢 😊

\#3

这周三早上先开车去媳妇儿医院复查了指标，吃了一周他汀后血脂指标明显好转；媳妇儿有顺带给我拉了心电图，说，心电图也好起来了，应该早点让我吃药的 🤡

于是就又开了好多药，后面可以继续吃，然后三个月后再复查一次

周三晚上选了老邻舍，请几位同事吃饭，主要是欢送蔡司令月底就要去美国了

最近两周吃的大餐太多，感觉还是老邻舍最好吃，嘿嘿

\#4

周日下大雨，老婆在犹豫要不要带秋宝去他们科室团建。

最后决定是，就我和她去就行了，连续大雨，而且天气闷热，带着秋宝实在有点不方便。

后来印证这个做法是正确的。

首先天气实在太闷热了，光坐着就不是很舒服，尤其他们还选择烧烤，几个烤炉一架，温度立马就上去了，我狂狂出汗。

而且场地给的肉质实在太差，他们自己带的肉实话说，也就一般水准。

其实主要还是天气太差，又闷又热还下着大雨，整体体验到大打折扣。甚至不如前年年初富阳别墅那次

所以我和媳妇儿直接提前开车回家了；结果紫之隧道最后半段还TM严重堵车了，整个人一个大无语

## Work

\#1

**CppNow 2025 | Parallel Range Algorithms - The Evolution of Parallelism in C++ - Ruslan Arutyunyan** https://www.youtube.com/watch?v=pte5kQZAK0E&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=41

- std::ranges 下面那些带 execution policy 的并行算法的演进
- 除了知道有哪些算法可以走 execution policy 之外好像也没新知识 get 了

**Defining interfaces in C++ with ‘concepts’ (C++20)** https://lemire.me/blog/2023/04/18/defining-interfaces-in-c-with-concepts-c20/

- 介绍 c++ 20 的 concept，可以做模板参数的约束

    ```cpp
    template <typename T>
    concept is_iterable = requires(T v) {
                            { v.has_next() } -> std::convertible_to<bool>;
                            { v.next() } -> std::same_as<uint32_t>;
                            { v.reset() };
                          };

    template <is_iterable T> size_t count(T &t) {
      t.reset();
      size_t count = 0;
      while (t.has_next()) {
        t.next();
        count++;
      }
      return count;
    }
    ```


**Defining interfaces in C++: concepts versus inheritance** https://lemire.me/blog/2023/04/20/defining-interfaces-in-c-concepts-versus-inheritance/

- 比较同样的代码使用 concept 和 virtual function 的差别，但是这里是概念上的更多一些，没有实际的 assembly 或者 bench
- 用 concept 一个好处是 template code 碰到优化良好的编译器，很多操作直接变成 compile time 或者大大减少了步骤

**Using shared_ptr for reloadable config** https://ddanilov.me/usage-of-shared_ptr

- 生产端定期读上游，创建 config 对象，atomic_store 写道 shared_ptr 里
- 消费端 wrapper 这个 const shared_ptr&，每次用的时候 atomic_load
- 但是我觉得这个用法很奇怪，而且因为每次 field get 都要先 atomic_load 一下 shared_ptr，抛开开销不说，有可能前后两次 load 的数据不是同一个版本的；而且代码里面为了避免每个 field 返回出现 dangling ref（上一个 shared_ptr 销毁了），返回的是值类型，就得拷贝
- shared_ptr 本身引用计数，为什么不每个 Config 对象都存一份 shared_ptr copy 呢？这样依赖，多个 fields 能保证是通过一个版本的，而且还可以返回引用；直接变成 copy-on-write 那套模型 就好了

\#2

这周在研究怎么把之前 conanized packages 通过公司 artifactory 的 prompt 机制给弄到 vendor-release center 上

然后又顺利的踩了俩坑，这俩应该都和 build team 搞得非主流的 prompt 机制有关

他们设计的机制是（我根据行为推测的）：

1. 触发构建的分支必须要是 `release-` 为前缀
2. 但是构建还是先在 conan-dev center 上完成，并且此次构建出的产物如果 remote 上没有对应的，则触发 prompt 流程；否则跳过
3. 但是是否需要构建比较的是 conan-release conter 的记录

然后对于 nlohmann_json 这样的 header-only library 来说，产物只有源码没有二进制，所以会判断和之前用 non-release branch 触发的构建的产物是一致的，所以直接跳过了 prompt...

最后在 Opus 的帮助下，做了一个 package build marker 的 trick：开关打开的话，会在产物里强行写一个新文件，内容每次是新的就行，这样来强制保证每次包产物不一样...

另外发现一个很神奇的情况，release 触发构建的时候，如果某个包 foo 依赖了 boost，因为 boost 有额外的 dependencies.yml，如果构建 foo 的时候不把 boost 加进去，就会发现 conan 自己 download 的 boost cached package 里面是没有这个文件的...

原因我也不懂是为什么，可能和 release prompted package 不会包含非产物内容？

因为找到了 workaround 了所以也懒得深究了

预计下周就可以给 scheduler 切构建系统了

不过可能还要专门解决一下 corp-jenkins docker image 构建的时候怎么 conan login，因为我们组的 build image 都是在公司的 image 上再跑 docker image 完成的，所以公司 build image 设置的 conan profile 到了里面估计会有问题。

---

好了这周就这样，下周见。
