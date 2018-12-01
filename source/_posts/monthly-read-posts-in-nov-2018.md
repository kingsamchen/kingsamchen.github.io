---
title: Monthly Read Posts in Nov 2018
categories: PROGRAMMING
date: 2018-12-01 15:55:31
tags: [shuffling, concurrent programming, golang, noexcept, cache server, api design, pthread, philosophy]
---
## Algorithms

[Shuffling](https://blog.codinghorror.com/shuffling/)

Writing a robust shuffling algorithm implementation is hard, even if you know how Knuth Fisher-Yates shuffling works.

Must be careful about random number generator.

---

[The intuition behind Fisher-Yates shuffling](https://eli.thegreenplace.net/2010/05/28/the-intuition-behind-fisher-yates-shuffling)

在直观上解释了 fisher-yates algorithm。

里面举的魔术帽取球的例子非常赞，结合后面的 reasoning 一下就能直观上理解。

实话说，算法类的东西，如果不能直观上理解的话，真的基本转头就忘了。

## Concurrency

[并行编程中的“锁”难题](http://www.parallellabs.com/2011/10/02/lock-in-parallel-programming/)

此篇略水，可作为娱乐消遣看看。

## Programming Languages

[Practical Go: Real world advice for writing maintainable Go programs](https://dave.cheney.net/practical-go/presentations/qcon-china.html)

QCon 上的一个 golang 分享，涉及面比较广。

不过感觉讲的东西都挺一般....

---

[Using noexcept](https://akrzemi1.wordpress.com/2011/06/10/using-noexcept/)

很赞的文章，从为什么引入 `noexcept` 到这个特性到这个特性经历的几次变化。

最后还介绍了 `noexcept` 的一些使用建议。

---

[Unique and shared ownership](https://akrzemi1.wordpress.com/2011/06/27/unique-ownership-shared-ownership/)

文章有点老了（2011年）但是几个关于 resource / RAII 的展开还是可以的。

---

[Brace, brace!](https://akrzemi1.wordpress.com/2011/06/29/brace-brace/)

核心两点：
1. `{}` 带来的新的 initialization syntax
2. default initialization & value initialization（包含 zero-initialization）

几个 initialization 间的差异在 03/11/14 几个标准间有差异，可以参考 https://stackoverflow.com/questions/29765961/default-value-and-zero-initialization-mess

## Web & Network

[Building a Simple Cache Server in Python](https://alexanderellis.github.io/blog/posts/simple-cache-server-in-python/)

前半部分简单介绍了一下要做的 cache-server 所起的作用，其实就是 users 和 original server 之间的一个 cache-proxy。

然后用 python 写了一个很简单的例子。不过这个例子看起来非常非常的不专业....

- max listening queue 居然是1
- 不支持并发请求
- 获取缓存的方法是直接读文件，而且这里的 disk io 是直接 blocking 的...

另外从这个 cache-server 起的作用看，更像是类似于 CDN 一类的大缓存站？因为缓存数据还是直接存在磁盘上。所以这一层缓存看起来只是减少了请求源服务器的压力而已。

## Misc

[pthreads as a case study of good API design](https://eli.thegreenplace.net/2010/04/05/pthreads-as-a-case-study-of-good-api-design)

作者在文章里头提到，因为 C 缺乏很多抽象的工具，因此用 C 实现优秀的 API 相比较其他语言存在本质困难。

而优秀的 C API 中 pthreads 是一个优秀的代表。

实话说，pthreads 的接口设计的的确简洁且优雅。

---

[Tcl 和 Raft 发明人的软件设计哲学](https://mp.weixin.qq.com/s/l_xnOd2gmTSbj3WqZAL7aQ)

problem decomposition -- control complexity

deep class 和 shallow class 那段是不是和上面这篇 pthread 接口设计的文章相得益彰?

另外通过语义变更来消除异常我自己之前也总结过这个，原因还是因为在 C++ 里使用异常的心智负担比较高，所以自己总结了一些方法论在正确的控制异常。

战术 & 战略之争是一个永恒的话题，尤其对快速发展小公司来说。
