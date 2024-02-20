---
title: 一周杂记 in Week 3 Feb 2024
categories: CODE-LIFE
date: 2024-02-19 22:00:00
tags: [杂记]
---
本周（02/12 ~ 02/18）是二月份的第三周，也是农历新年假期将进入尾声的一周。

## Life

\#1

初三约了两个发小（小学+高中同班同学）回了趟高中母校，在学校里瞎转悠了一圈，边走边聊。

好像除了封控的那两三年外我们保持这种习惯已经好多年了；再次感慨一下时间过得好快啊

初四我们家做东，定了三张桌结果好多堂/表兄弟姐妹或因为外出旅游或因为工作事务缠身来不了，于是匀了一桌出去，剩下的人稍稍散开坐才算勉强撑住两张桌 😅

中午吃完饭后回家稍微整理了一下就坐高铁回杭州了，毕竟媳妇儿第二天就得值班了...

\#2

年前考了驾照之后就没碰过车了，过年期间想拿家里的车练一把找找感觉。

初三去发小那天刚好逮到机会，拿家里那辆二十年的老天籁来回耍了两把。

虽然我妈坐副驾但是没有副刹车所以一开始还是比较谨慎的，油门都不怎么敢踩。

不过实话说自从我用那辆手改自的教练车开了7，8百公里之后我现在觉得啥车都好开...家里那辆老天籁油门加速反馈几乎是一脚下去就有，方向盘也“跟手”很多

就等今年自己买车了，😄

不过吐槽一下温州小县城大多数人乱开车的现象，尼玛你们命贱老子的命可金贵着呢

\#3

过年期间吃得太多直接给吃胖了，回到杭州一上秤好家伙直接到了73.x kg

立马安排健身计划，恢复有氧和器械

好消息是回来第一次有氧就把 1000km 的里程碑达到了

初七和媳妇儿吃了一次荆九爷，好像是湘菜？味道确实不错

而且因为前两天媳妇儿睡太多了说想走走，结果没想到我们走着走着走了5公里多直接走回了家 😎 然后媳妇儿到家就从中午睡到了傍晚 🤪

回杭州这几天大多天气都不错，心情也开心许多

\#4

本周观影记录：

- **非诚勿扰3** 2/5 要不使用阿B大会员看的我真的要喊：“日内瓦退钱” 了。 广告植入这么多是担心票房回不了本是吗
- **飞驰人生** 3/5 前面小品感太强了不够流畅，后面赛车戏还不错
- **帝王计划：怪兽遗产 _Monarch: Legacy of Monsters_** 2/5 虽然 godzilla 一开始是冲着反战思潮创作的，但是你都要做衍生电视剧了欸，能不能不要在S1就把叙事重点放在家长里短上，我寻思这标题也不是速度与激情啊。另外Cate这个大女主是真的烦，角色弧度都变成stereotype了都；两代 Russell 倒是有点意思

## Work

\#1

本周学习进度

**Design Patterns in Modern C++ | 3. Factories**

- 介绍传统的 OOP factory method & factory class 以及干了十多年没见过真有产品代码再用 abstract factory
- 章节最后介绍了利用 `std::function<>` 来包装 creation logic 实现 factory method 还算可以
- 但是中间使用 shared_ptr/weak_ptr 那段我觉得不是一个合理的 use case

**Design Patterns in Modern C++ | 4. Prototype**

- Prototype 模式就是对象的 deep copy & premade
- 对于 C++ 来说其实这个 pattern 没太大存在感

**深度学习入门 | 3. 神经网络**

- 从感知机衍生到神经网络，区别在于使用的激活函数不同，并且介绍了 softmax；点了一下为什么激活函数必须要用非线性函数
- 从简单的一维参数扩展到矩阵参数
- 机器学习两大核心问题：回归和分类

**价值投资实战手册 | 2. 如何估算内在价值**

- 分别介绍了格雷厄姆的烟蒂投资理念和巴菲特后来衍生出的价值投资观点
- 其实二者本质相同都是找当前价值低点的企业，后者更多一些主观/主动挑选的意味
- 这章不少理念挺合我胃口，比之前一些“小年轻”写的刻舟求剑的书靠谱多了且可操作性也更强

**Why does one NGINX worker take all the load?** https://blog.cloudflare.com/the-sad-state-of-linux-socket-balancing/

- 从 clouldflare 线上出现 ngx 示例出现负载不均衡开始谈起，干活非常多
- blocking `accept()` 调用在 Linux 内核中用的是 FIFO 调度，所以如果多个进程 `accept()` 同一个 listening fd 会具有天然分散负载的效果
- 而如果使用 epoll + non-blocking 多个进程 `accept()` 同一个 listening fd 则会出现明显的负载倾斜，因为内核对 epoll 的处理上是选择 LIFO 策略，优先选择上一个被选择的执行上下文
我记得 Windows/IOCP 也是这个策略，猜测可能和 warm cacheline 有关
- 使用 `SO_REUSEPORT` 可以做到 epoll + concurrent accept 下的负载均衡，但是这又会导致 overall latency 的增加，尤其会出现更多的 long latency
- 目前没有太好的解决方案，除非内核改变 epoll 的上下文选取策略
- 另外文中还谈到 Linux 下因为内核实现问题，blocking concurrent accept 其实是有问题的

**How we scaled nginx and saved the world 54 years every day https://blog.cloudflare.com/how-we-scaled-nginx-and-saved-the-world-54-years-every-day/**

- 使用 nginx 作为反代服务，event loop + non-blocking 保证 C10K 轻松可解，但是必须要减少 event-loop 的阻塞情况
- 用 SSD 加速磁盘 IO
- 使用 `SO_REUSEPORT` 来提升连接处理的负载均衡
前面有同样 cloudflare 的文章提到使用 SO_REUSEPORT 来处理建立连接会稍微增加 long tail latency & overall latency，但是文章里的主要场景都是长链接，这部分延迟增加可以忽略不计
- 磁盘文件的 `read()` 很耗时，`open()` 同样很耗时，甚至小文件里比 `read()` 还耗时，都扔到 thread pool 里跑

**CppCon 2021 | Back to Basics: Pointers - Mike Shah**

- pointers 基础教学，非 newbie 可以快速翻过

**CppCon 2021 | Compile-Time Compression and Resource Generation with C++20 - Ashley Roll**

- 一个比较“有趣”的talk，作者用 compile-time tricks 实现了 resource generation 并且通过定制的 linker script 把这部分资源放到了 executable 特定位置
- 还实现了 compile-time string compression (based on huffman tree) 并且提供了一些实现这个 string compression 需要的基础类
- 相关代码作者放到了自己的 repo https://github.com/AshleyRoll/cppcon21
- 感觉 constexpr/consteval 这些 compile-time features 被玩的越来越花了

**CppCon 2021 | Practical Advice for Maintaining and Migrating Working Code - Brian Ruth**

- 总结一下就是：如何同屎山为伍；作者感觉是 G 家 android 团队的，经历应该还是有说服力的
- 包括了如何给 legacy code 加 unit tests：一点点来，先从新增code开始，必要时可以对老函数做一点 hijack，后面慢慢把函数弄规整。
还有一些设计便马上的技巧规范一类的，操作性还比较强。这类小做法的目的是为了实践 boy scouts 原则，即离开一段你动过的代码时这段代码应该比你动手前更干净，哪怕只是干净一点点

\#2

自从几年前基于 LLVM style 定制过一次 `.clang-format` 之后就一直用到了现在，clang-format 也从当时的 v11 不声不响的升级到了现在 v17

有点好奇是不是有什么新增的 options 可以更贴近我的喜好，顺带看看能不能解决之前没法检测 line-length 过长的问题。

于是把 clang-foramt style 从 v12 开始的 options 一个一个扫了一遍，根据喜好往 `.clang-format` 里新增了几个规则，又跑了一次全量的 reformat，结果发现前后没有任何格式上的变化，看来我确实是一个前后喜好比较一致的人 🤣

至于 line-length 控制的问题目前看起来也没法仅靠 clang-format 解决。

虽然 clang-format 有个 option 叫 `ColumnLimit`，但是如果设置成一个不为0的值，reformat 的断行行为是根据一套复杂的算法来计算的，就算某一行没有超过这个 limit 也会由于断行算法而被强行断行。

而蛋疼的则是某些 context 下控制这个断行的选项还不存在，这就导致如果我想依赖 `ColumnLimit` 保证不超行就要解决他的断行喜好和我不自己不一致的问题。

不仅要一个点一个点 fine-tune 过去，而且很可能出现某些代码段无法被微调的情况。

思考再三（强迫症犯了），决定放弃使用 `ColumnLimit` 来约束 lien length，转而考虑使用 cpplint 来 warn 行超长的情况。

因为（现在的）我不太喜欢 google c++ style，所以我把 cpplint 除了 `whitespace/line_length` 之外的 rule 全关了，稍微调整一下之前的 pre-commit hook 就可以做到代码提交时同时检测 style 和 line length。

（后面可以考虑打开 cpplint 几个和我偏好不冲突的 rules，比如 include headers 啥的）

这个变更直接做到了 esl 里，见 PR：https://github.com/kingsamchen/esl/pull/13

\#3

之前先后看了 Christopher Kohlhoff 写的 _system error support in C++0x_ 系列以及 _Boost.System_ 的文档，感觉自己对 `error_code`/`error_condition`/`error_category` 比较了解了。

但是当我在工作中打算用这套机制实现错误汇报/处理的时候发现我对多个细节点还是一脸茫然，依然感觉有点无从下手。

所以打算这几天重新翻阅一下这两个资料，顺便写一个能在生产环境借鉴的 PoC（而不是那种烂大街没啥用的 simple sample）

有句话咋说的：纸上得来终觉浅，绝知此事要躬行

所以还是得自己亲手做一遍才能获得深刻的理解 😆

---

好了这周就这样，下周见
