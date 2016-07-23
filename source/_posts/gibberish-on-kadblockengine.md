---
title: 闲扯 KAdBlockEngine
date: 2016-07-23 16:52:55
categories: CODE-LIFE
tags: [gibberish]
---
几天前给 [KAdBlockEngine](https://github.com/kingsamchen/KAdBlockEngine) 加上了 README 之后，这个 repo 算是正式的结了。

对别人来说，这只是一个很普通的 repo；但是对我而言，却意义重大。

3年前人生第一次实习，最主要的任务就是重写某浏览器的广告过滤引擎；那大概是我第一次写真实产品的代码。

虽然之前几年在大学里看了一些书，写了一些小打小闹的代码，但是由于还不够努力的缘故，那几年的训练并没有让我形成工程实践相关的思想和认知；而当时的 AdFilterEngine，则是我第一个有意识的，开始试图形成自己的方法论的项目。

觉醒总伴随着代价，如果以现在的认知去审视 AdFilterEngine 的设计和代码，会发现许多 twisted thoughts 的痕迹，但是这些都无法掩盖 AdFilterEngine 是我职业道路上的一个非常重要的起点。而起点过后，则是非常迅速的能力上的提升。

（这里要感谢当时浏览器团队的那帮同事，共事的那段时间是我职业生涯非常重要的一段时间）

在后来的一段时间里，我有多次想过重写这部分代码，但都未能付诸行动，或许是恐惧自己达不到当时的期许吧。

转眼到今年年初离职，离开北京来到上海，我觉得自己的人生怎么也算过渡到一个新的阶段了，于是内心又开始惊起重写 AdFilterEngine 的波澜，不同的是，this time, I took the shot，于是就有了 KAdBlockEngine。

我的第一个提交发生在4月9号，最后一个提交（加上 README）发生在7月19号。

期间断断续续地提交了一些代码，让我有足够的时间去构思设计；并且在重写过程中，因为一些设计需求，我针对 [kbase](https://github.com/kingsamchen/KBase) 的几个模块也同时做了重构。

首先是为了（更好的）处理文件路径，重构了 [Path](https://kingsamchen.github.io/2016/04/19/make-our-path-class-more-natural/)。

接着为了更能够高效的从规则文件中抽出规则文本，以及简化一些规则文本的预处理，加人了 [StringView/Tokenizer](https://kingsamchen.github.io/2016/05/03/an-unofficial-implementation-for-string-view-and-its-companion-tokenizer/)。

考虑到对 filter 的序列化保存，避免每次都要重新解析规则文本，重构了 Pickle；并且在过程中[加深了对 resource ownership 的理解](https://kingsamchen.github.io/2016/07/10/do-not-intermingle-owner-with-view-semantics/)。

（而序列化让一个 filter 的初始化时间，在 debug 模式下，从 7s 降到了 500ms）

在这几年的训练下，KAdBlockEngine 不仅在设计/代码上更加合理、优雅，性能也得到了提升（配合增加的 StringView 和 Tokenizer，我可以说是在这部分代码里真正做到了省掉了不需要的拷贝）；一定程度上可以算是这两三年来进步的见证吧。

人是一种奇怪的动物，时常会回想过去；在重写 KAdBlockEngine 期间，我不止一次想起自己刚开始实习，以及还在大学的种种瞬间，不只一次觉得：嗯，当初要是能够在努力一点该有多好呢。