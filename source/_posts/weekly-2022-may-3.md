---
title: 一周杂记 in Week 3 May 2022
categories: CODE-LIFE
date: 2022-05-22 23:23:17
tags: [杂记]
---
## Life

\#1

可能是最近接连几个政策刺激，本周基金和理财都有不同程度的回血，希望势头至少延续到5月结束，毕竟今年头几个月实在太难看了。

当然整体上我还是看衰后续的经济数据，就希望不要太明显拖累债市和理财就成。

\#2

周五下午和老婆跑去长龙（老婆的房子）住了一晚，萧山那边还是太远了，而且在建的工地数量明显比城西这边要多。

不过新城区一个好处是楼都比较新，马路也比较宽，如果自己开车的话算是加分项吧。

考虑到周五的时候发现家前面那块荒了3年的地终于开始动工了，估计未来3-5年都没啥好日子可以过了，大白天噪音为伍的日子应该跑不掉了。

现在就寄希望于6月份买了新路由器在家里组上 mesh 之后把书房搬到次卧（现在老婆的书房），那边好像噪音可以小很多。

\#3

周六下午的时候老婆突然说想看古埃及相关的电影，然后就一阵搜索选了木乃伊归来（木乃伊 2）。

找了一下资源发现居然有 2160P 的蓝光，115 离线拖了快俩小时，又找了一个匹配的字幕，往大法的电视上那么一放，画质是真没话说。

我上一次看着电影都快二十年前了，所以这回权当半温习。不过老婆觉得古埃及的部分太少了，现代的戏太多，不太满意...

## Work

\#1

这周 lumper 的主要进度是加上了 cgroup manager 和 memory limits。

期间遇到三个坑。

第一个是自己创建的 cgroup，例如 `/sys/fs/cgroup/memory/lumper-cgroup` 这个目录没法用 `std::filesystem::remove_all()` 删除，提示 operation not permitted。

搜索了一下发现因为目录的文件其实是假文件，`remove_all()` 会首先尝试删除文件，所以会失败。

正确的姿势是直接使用 `rmdir(2)`。

细节见 https://github.com/kingsamchen/Eureka/blob/master/Lumper/lumper/cgroups/memory_subsystem.cpp#L37

第二个坑是之前的 `lumper run` 的 command parse 逻辑有点问题。

对于 `./lumper run --it some-executable --flag` 这种命令，会把 `--flag` 当作 sub command `run` 的 flag，导致解析失败。

解决方案是把原来的 `CMD` 和 `ARGS` 统一成一个处理。

见 https://github.com/kingsamchen/Eureka/blob/master/Lumper/lumper/cli.cpp#L58

第三个坑是每次执行完 lumper 之后，当前 session 自己的 `/proc` mountinfo 就对不上了，需要手动执行 `sudo mount -t proc proc /proc` 复位。

在 _自己动手写 Docker_ 这本书的官方源码仓库搜了一下 issue 果然发现有人反馈了类似的问题。

原因是新内核下的 systemd 会默认的把哪怕是 namespace 隔离下的 new mount 共享到外部。

PR：https://github.com/iamwwc/wwcdocker/pull/4

相关的 man：https://man7.org/linux/man-pages/man7/mount_namespaces.7.html#NOTES

照着 NOTES 里的说法在 new mount 前把 `/` 设置为 private 就可以了。

\#2

本周虽然没有抽时间看书，但是把放了很久的 Linux 动态链接库和 PLT/GOT 相关文章给看完了，并且写了总结笔记。

从某个角度上来说也算是 reading 部分把 XD。

\#3

工作上这周主要在折腾 reindex consumer。

做完了 part 1 虽然被 storage layer 的接口设计给恶心了一把。

后续的 part 的难点主要在要不要做并发处理。

因为周五看 consumer 框架代码时候发现现在 consumer 现在其实是多 worker thread 消费的 tasks。

额外再针对某个 task 做更进一步的并发处理一定要考虑到现有的 worker threads，至少要从并发度和总工作线程数上考虑。

假设现在有 K 个 worker thread 并行消费，每个 worker thread 再额外开一个大小为 T 的线程池显然有点不太合适；K * T 个活跃线程对调度可能都是个负担，而且仔细分析一下，consumer 大部分时间应该都是在等待 IO 上。

而如果这 K 个 worker thread 共享一个额外的线程池，那么这个线程池至少也要有 K 个工作线程，否则消费的并发度会下降；而且这种方案可能会出现任务饥饿的情况。

具体设计还需要再 refine 一下。

不过我们当前的一个问题是，虽然我们有 thread pool，但是没有 event loop...

---

这周就这样，下周见
