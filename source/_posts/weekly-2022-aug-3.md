---
title: 一周杂记 in Week 3 Aug 2022
categories: CODE-LIFE
date: 2022-08-27 12:05:38
tags: [杂记]
---

这周其实是8月的第四周了，但是因为上个周末一些事情耽搁了，加上工作日比较忙，所以上周的杂记一直没来得及写...趁着这周周末还没结束，先<del>悄悄</del>补上。

所以这是8月分第三周的事情 🤣

## Life

\#1

上周的湿疹在英明神武妙手回春的老婆开的药的治疗下，出现了明显好转，但是为了保证恢复质量，所以基本没有怎么运动，保证患处干爽。

看恢复态势，周末结束就能完全痊愈，是个好消息

\#2

因为周二（北京时间）Better Call Saul 最终季最终集释出，所以周三特意请了一天假在家把最终季下半部分全看完了。

只能用一个心满意足来表达，很过瘾，结局不仅没有烂尾还堪称完美。

Kim 又开始了做自己，Jimmy 也和过去和解，主动选择待在监狱救赎。

除了 BCS，这周还额外看了两部电影：_The Hitman's Wife's Body Guard_ & _Doctor Strange: the Madness of Multiuniverse_

前者是当年 _The Hitman's Body Guard_ 的续集，但是整体感觉比前作差太多，满篇的插科打诨没有提升整体效果，反而显得很聒噪。

奇异2 疯狂宇宙（🤣）比预想的要好，整体叙事很流畅，山姆雷米的恐怖要素该有的也都有。唯一坑点只能说是把前面的 wanda vision 给摆了一道导致 Wanda 角色没立住...

要是 wanda vision 换一个黑暗点的结尾，或者电影参考现有的结尾做一些调整会好得多。

\#3

这周墙内的政治经济局势一如既往的微妙...

## Work

\#1

看了两篇讲 TOPT (Time-based One Time Password Algorithm) 设计的文章，大致了解了 TOPT 的整体流程。

1. [How Google Authenticator Works](https://garbagecollected.org/2014/09/14/how-google-authenticator-works/)
2. [HOW DO ONE-TIME PASSWORDS WORK?](https://zserge.com/posts/one-time-passwords/?utm_source=pocket_mylist)

Google Authenticator 就是一个非常典型的 TOPT 应用。

于是很自然的打算拿 CXX 也写一个实现，毕竟 learning by doing 嘛，可没成想遇到两个坑。

第一个坑是 CXX 的标准库没有内置 base32 codec 支持，所以需要找第三方库，我倾向于用那些 lightweight 的，最好是 modern C++ 的，于是看上了 [cppcodec](https://github.com/tplgy/cppcodec) 这个 header only lib。

但是用的时候发现 CPM 怎么也没法引用到这个 lib 的头文件，于是检查了一下他的 CMakeLists.txt；好家伙，发现这个 CMakeLists.txt 写的有问题，没有给自己的 lib target 增加 include directories 属性。

本来打算修一下提一个 PR，但是瞥见 PR list 里有一个一年前的 PR，看了一下发现这个 PR 写的也不是很对，于是给提了一点 [comments](https://github.com/tplgy/cppcodec/pull/73)。

没想到（疑似）作者第二天就给了回复，并且因为之前这个提 PR 的人并不是 PR 的原作者，所以库作者重新提了一个 [PR](https://github.com/tplgy/cppcodec/pull/76)，引用了原 PR，还在其中增加了对大家的感谢。不过目前这个 PR 还没被合入 master。

另外一个坑是 TOPT 需要用到 HMAC-SHA1，同样的需要找一个三方库。

找来找去发现比较合胃口并且不太大的库只有 cryptopp，但是这个库更牛批，连 CMake 都没提供....

无奈想了一下要不试试 vcpkg 和他的 manifest mode 来看看能不能简化流程。

于是找了一下 vcpkg 的官方文档，加了一个 vcpkg.json 描述两个依赖之后发现意外的体验还不错；而且上面说的两个库都可以很顺利地使用。

所以后面考虑多用用 vcpkg 来管理依赖，以及调整一下 anvil，让生成的 cmake project 模板可以将 vcpkg 的支持作为一种可插入的模块。

\#2

这周书看得少，主要集中在比较零散的内容摄入上。

- **《透视HTTP协议》** 依旧一天一课，感觉这课非常适合新手入门学习。打算后面给自己带的 mentee 补一下基础
- 看了一篇 paper **_Fuss, Futexes and Furwocks: Fast Userlevel Locking in Linux_** 但是感觉收获不是很大
- **_CppCon 2020 | Cross-Platform Pitfalls and How to Avoid Them - Erika Sweet_** 核心其实是 vcpkg team 的迭代心路历程，值得推荐。我也是再看了这个 talk 之后想去再试一试 vcpkg

---

本周就是这样，下周见
