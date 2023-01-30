---
title: 一周杂记 in Week 4 Jan 2023
categories: CODE-LIFE
date: 2023-01-30 21:43:56
tags: [杂记]
---
本周是1月份的第四周，春节将于本周正式结束。

## Life

\#1

美好的假期过得特别快，舒服的假期终于还是结束了...

初四下午回到了杭州，和媳妇儿一起吃了个炸鸡当晚饭，初六和发小一起看了电影，初七老婆值班，然后又除了一些大事情，下面细讲。

\#2

初五的时候两个发小（小学同学兼高中同学）问要不要初六一起看流浪地球2，问了一下老婆有没有兴趣，她说她没兴趣，于是就爽快答应了发小。

电影的评价我就搬一下我 twi 上的吐槽好了

> 电影在特效、节奏把控和剧本流畅度等方面做的比1是有级别的提升，可以说是中国电影工业的扛鼎之作，演员们的台词水平也终于不拖后腿了。评价上再也不需要滤镜加成给予鼓励了。 但是最后一个小时真的让我如坐针毡。
> 最大的问题就是味儿太冲了，集体优于个人，个人又不是人，完了还地球是我家，未来靠大家…和我个人信条差异太大。
> 李雪健的周总书记还不忘强行点火给世界指明方向，要不是剧本提前规定好这总书记的位置确实应该让您来。没准推送全世界动态清零成功呢 🤡
> 另外刘德华的图恒宇这个角色也是一个很奇怪的存在，总感觉是导演做的一种折衷。

总体评分 3.5/5

\#3

初四刚到家老婆就说她奶奶因为新冠后遗症呼吸不畅导致昏迷被拉到温附医ICU了，当时总担心老人家熬不过两天。还好第二天人就醒了，也恢复了意识。

本来让奶奶在温附医继续治疗就差不多能康复等事情翻篇了，没想到因为担心治疗费用又搞出各种幺蛾子。

这个细节就不说了，实在是一把辛酸泪，人为💴折腰。

## Work

\#1

这周在老家以及回杭州后花了不少时间仔细研究了一下之前搞得 clang-format-diff hook 和 run clang-tidy on build，因为切换工作环境之后发现了几个问题。

1. pre-commit hook 里执行的是 `clang-format-diff.py` 但是发现 ubuntu/mint 下安装 clang-tools 并不会默认创建这个文件，而是有个 `clang-format-diff-VER`，这个文件其实就是个 python script，但是文件格式不对，并且没有在 alternatives 里安装 clang-format-diff.py
2. Windows 下生成的 MSVC 工程构建时实际上不会跑 clang-tidy
3. 升级到 clang-tidy-15 之后增加了好多 rules，之前 esl 好多代码都开始被 complain 了

所以做了几个 PR/commit 来处理这几个问题 https://github.com/kingsamchen/esl/pull/1 和 https://github.com/kingsamchen/esl/pull/2

注：适应工作上所有提交都通过 gitlab MR 的工作流之后，我也越来越习惯把 non-trivial 的提交搞成 github PR 了，PR 的描述可以显示更多的信息。

\#2

本周学习总结：

书这边

- _Software Engineering at Google_ 看完了 _23. Continuous Integration_
  因为工作上目前都是差不多这么来的，所以就觉得：是啊，就该这么搞啊。
- _Database Internals | 11. Replication and Consistency_ 看了一部分
- 极客时间课程《kafka 技术实践课程》看了几课
  我有预感这门课后面干活会不少

顺带看了一个 slides：_Memory Allocation Either Love It or Hate It_

看完之后的感想就是：没事就不要自己写 allocator 拉。

CppCon Talk 看了一个 _CppCon 2020 | The Shapes of Multi-Dimensional Arrays - Vincent Reverdy_

其实就是 span/mspan/dspan .etc 的一个 design PoC

不过我个人对这个目前不是很感兴趣。

---

好了这周就这样，下周见
