---
title: 使用 ffmpeg 压制视频
categories: PROGRAMMING
date: 2017-04-12 00:58:45
tags: [C#, ffmpeg, 视频压制]
---
帮主站重写完投稿工具的上传模块后，Neo 和我说，我们这期版本还是得带上视频压制功能...

这是我第一次知道原来 ffmpeg 还可以压制视频。因为重构的缘故，老版本的代码完全不能用（就算不考虑换上层 UI 框架的事儿，老版本那个代码质量...），所以只能抄一下他们的压制相关的驱动参数，自己从头把功能实现一遍。

花了差不多刚好一周的时间提供了一个底层压制模块后，我发现，因为产品需求的因素，实现这部分功能居然可以很好的做为熟悉一个语言涉及操作层面的 roadmap，整理一下 feature list，算算差不多至少要能够提供：
- 运行子进程，并且能够通过 IPC 读取并解析子进程的 stdout 和 stderr 数据；通常情况下，这意味着需要熟悉 Windows 的 Pipe
- 压制首先涉及到利用 `ffprobe.exe` 检测视频的 meta-info，符合压制条件并且用户确认后利用 meta-info 作为压制的部分输入信息；中间涉及到几个线程间的交互
- 因为压制功能和视频上传在逻辑上具有先后顺序，并且都需要有专门的 worker thread，因此他们可以共用一个线程池；而支持多任务并行操作，又要求线程池的调度策略要足够“及时”（事实上，这个要求有一部分锅来自 chromium net 的 `URLFetcher`，which requires 网络操作运行的线程在 URLFetcher context 生命周期内是唯一且固定的，因此会出现一个上传任务只要不结束，就会一直霸占一个线程的情况；另有一部分锅来自 chromium base 的 `scoped_reptr`，居然没有考虑对外暴露自己 ref-count 信息，无形中加大了实现线程池的困难）

因此在项目提测结束，基本验收通过之后，我想籍由这个功能，重新回忆一下 C# 和 WPF （噫，怎么老是再回忆这个...），所以花了一个周末，就有了 [SimpleVideoEncoder](https://github.com/kingsamchen/Eureka/tree/master/SimpleVideoEncoder)

但是在实现过程中我发现，纵使有 async/await，它也不是银弹，也有他覆盖不了的 case；而我对 C#/.NET 的多线程模型实在是完全不懂，导致某些细节完全没法下手（只能说某些case 下，CSP 真是好用到想哭，还好 WPF 的 UI-thread 提供了一个 `Dispatcher` 支持了对 UI 线程的 CSP；再将需求简化到 Demo 程度后，勉强实现了出来。

![](/img/ffmpeg-encode.png)

上层界面基于 WPF，但是没有走 MVVM。

实话说，经过一年半（距离上次写 EasyKeeper），自己主导过两个不大不小的项目后，对于为什么需要有 MVC/MVP/MVVM 有了一些更实际的想法，并且也能理解有时候过分追求这些反而是一种坑。

考虑到目前应该是没法公开为投稿工具写的那部分代码（压制模块算起来大概有 1K+ lines of code），所以这个 *SimpleVideoEncoder* 的意义也大大折扣，基本只能作为一个 ffmpeg 压制功能的简单展示...

不过最后还是要赞一下 C# 对 Pipe IPC 的封装，几行代码做了需要几十行 C++ 代码的事情；并且基于 event 的通知机制比起传统的 observer，更能准确的体现 OOP 中**对象级别消息通信**的内涵。