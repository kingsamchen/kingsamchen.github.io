---
title: 一周杂记 in Week 4 Feb 2022
categories: CODE-LIFE
date: 2022-02-24 22:42:33
tags: [杂记]
---

## Life

- 刷完了 The Expanse S05；这一季主要仍是在做剧情铺垫和完善角色形象，但是这已经是倒数第二季了，这个时候才开始要丰满主要反派 Marco Inaros 的形象未免也太晚了吧。何况还要顺带加上 Philip...这就导致这一季又重复了一遍之前的旧模式，缺乏点新意。
  另外这一季 Alex 的演员因为性骚扰丑闻被剧本杀了，Clarissa 加入队伍，不得不感慨一下。
  同时 Mrs.Avasarala 重新当选为联合国秘书长；以及 White Collar 里的 Peter 叔 [Tim DeKay](https://movie.douban.com/celebrity/1017978/) 打了个酱油，出演反叛的 MCRN 军官，结果最后一集在穿越 Ring 的时候直接被湮灭了...
- 刷完 S05 之后意难平，又迅速的刷完了 The Expanse S06...
  作为最终季只有六集，不得不说实在是太短了；并且很多支线伏笔都没有收，这明摆着是不得不暂时剧终。
  但是不管怎么样，最后一季节奏紧凑，视效过瘾，还算是令人满意。
  The Expanse 大概是过去二十年最棒的太空歌剧了；可惜成本高，收视一直一般，没能再继续往下实在是令人感到缺憾。
  听说小说还没完结，希望有生之年可以看到续作。
- 俄罗斯入侵乌克兰，又又又一次让我感觉这个世界越来越朝着糟糕的方向发展，哎...
  希望乌克兰人民能奋起反抗抵挡侵略；以及某些俄黄子孙真是太孝了。
- 找老同学 O 哥买了个块二手的 1050 2G，试了一下 Left 4 Dead 4K 中档画质毫无压力；双 4K 屏可以组装起来了，XD

## Work

- 读完了 [Good Practices in Library Design Implementation and Maintainence](https://github.com/kingsamchen/footnotes-of-linux-multithreaded-server-side-programming/blob/master/good_practices_in_library_design_implementation_and_maintainence.md)
  这份10页的 PDF 小册子主要是动态库的工程实践建议。
  某些条款例如尽量避免暴露内部数据结构，只导出必要的接口函数经过这么多年差不多已经成为一种基础常识了；但是维护那边的某些条款可能不太适合目前的后端开发。
  另外未来后端服务开发可能越来越不需要动态库了。
- 这周老板说我们需要抽出一半的时间来加入到客户端开发，帮助客户端早日 launch；毕竟大家唇寒齿亡
  但是刚开头 clone 客户端的代码就深陷泥潭，这群人凭借着“又不是不能用”的屎味主义，这几年来成功把 client 的 repo 搞到了10G ~ 20G 的规模，常规的 clone 完全没发成功，而 git 又不支持断点续传...
  经过摸索找到了曲线救国的方式：
  ```shell
    git clone xx --depth=1
    git fetch --unshallow # can skip if no need full history for default branch
    git config remote.origin.fetch "+refs/heads/*:refs/remotes/origin/*"
    git fetch origin desired-branch # must specify branch name, or will fetch all branches.
    git checkout -b desired-branch origin/desired-branch
  ```
  代码 clone 成功了，但是编译还有一堆问题...看来下周还要被编译坑一会儿...
- 因为 MTA 的 task 差不多都做完了，所以必须得开始做 async search consumer，接入 hefei 的 SDK
  因为技术栈是 Java + Spring Boot，抽了时间快速过了一下 spring.io 的几个 tutorials，顺带把 hefei 那边的 SDK 跑通了。
  和 Jarvis 做了任务拆分，他负责 bootstrap service，并且集成 SDK 和必要的依赖，我来接 rabbit-mq。
  这里真得感谢 Jarvis，不然我一个人搞这块没啥经验估计要被坑不少时间...
  Spring Boot 给我的感觉是 Java 什么的并不重要，重要的是要学会如何使用这个框架，以及在哪里填代码...
- 这周面了两个人；第一个 Server 岗的哥们工作十多年了，经历也比较丰富。但是实话说水平一般，没有太亮眼的地方；而且后端岗位HC所剩不多，但是竞争太激烈了，可以说卷到飞起，所以思索再三第一面我就给挂了。
  第二个是钉钉的客户端 P7，后来发现是大学同学 Sunus 坐对面的人...这个人面完我就觉得被我挂掉的 Server 的那个哥们要是面客户端得稳进啊...
  这位 P7 说话水平是真牛逼，要不是我给打断了能从开始扯到面试结束，但是技术水平实在太令人捉急了，老板说大家可以适当 lower bar，但是就算这样这哥们还差了好大一截。
  而且二面的同事还抓到了他作弊的证据....
- esl 的 strings/split 大体上快完成了：核心的 split_iterator 和 split_view 都 OK 了，就剩下最外部的包装函数。下周应该就可以搞定了
- 这周因为都在忙着写代码，所以没怎么看书；唯一抽时间看的还是一本坑书...这个回头有时间再补吧

---

这周就这样，下周见
