---
title: 推荐 google samples for android-architecture
date: 2016-04-25 01:13:57
categories: PROGRAMMING
tags: [android, MVP]
---
在 Android 正式支持 data binding & MVVM 之前，MVP 可以算是最好的 android app 架构模式。

但是直到前不久，Google 才在 [github](https://github.com/googlesamples/android-architecture) 上提供了推荐的 android-mvp 做法。

虽然现在能找到各式各样实现 MVP 的 blog posts 和 samples，但是不得不说 Google 这次提供的 sample 依然惊艳十足，并且还是来自官方钦定。

Sample 将 view layer 和 presenter layer 需要协定的接口写到了一个单独的 xyz_contract 文件里，view 和 presenter 都需要实现各自的接口。

Sample 中 view layer 对应的是具体的 fragment，activity 只是充当了一个 startup facility。我觉得这个里可以做一个灵活的选择：如果引入 fragment 是没有必要的，那么可以直接将 view layer 放到 activity 中。

Model layer 的实现到可以不用那么讲究，如果项目中提供了 dependency injection 的设施，或者需要单独对 model layer 做 unit test（一般有这种需求手头应该有支持 DI 的设施了），则可以在 presnter -- model 间协定一个 contract，present 持有实现了 contract 的对象，做到进一步解耦。

反之，如果不需要在 model layer 有太多的变化余地，则可以直接把具体的 model object 暴露给上层的 presenter。

讲真，如果没有 DI 协助，多一个 model contract 其实也挺多余。

Google samples 采用的是混合方式...看代码的话就知道了。

为了更快速的熟悉 google 推荐的做法，我花了点时间做了一个 [login demo](https://github.com/kingsamchen/Eureka/tree/master/Android-Login-MVP)。view 直接放在了 activity，并且 model 直接暴露给了 presenter。

举个例子：

因为 view 中不能包含除了 UI 设置以外的代码，所以 login button 是否可用状态是 view 层监听输入框消息，并且将事件 delegate 到 presenter，presenter 通过判断后通知 view（调用 view 暴露的接口），改变按钮状态。

整个流程拆解后虽然变得繁琐，但是责任却变清晰了，所以在业务逻辑变得复杂的时候，这种 view -- presenter 的拆分会逐渐体现出优点。

不过很明显，如果采用 MVVM，上面的实现会变得更加优雅、简单。

最后还是推荐一下 android devs 看一下这个 demo（虽然我不是很想做 android app -''-）。