---
title: 一周杂记 in Week 4 Sep 2025
categories: CODE-LIFE
date: 2025-09-29 22:43:24
tags: [杂记]
---
本周（09/22 ~ 09/28）是9月份第四周。

这周有十一的工作调休，下周正式十一假期；不过这周气温回升，好几天都35-6，又是酷暑的感觉。

## Life

\#1

这周 work stream 算是有进度但是进度一般，一面要赶紧赶在周日和周一美国那边上班前把 Jenkins 至少搞稳定，一面是美国那边的一些 MR 一合就炸。

所以这一周基本处于美国上班的时候拉屎，我们上班的时候帮他们 cleanup 的状态。

不过好消息是周日的时候这个 Jenkins 已经开始绿了，除了偶有 flaky tests 导致的失败

这样看来美国周一时间上班前还是有希望的。

\#2

周四下午请了半天假，中午开车去媳妇儿家准备下午带娃去社区打疫苗。

然后一家人到了社区医院，医院说距离上次接种还没到30天，不能打...

于是我们又回来了，约等于白出来了一趟。

不过看起来娃因为对外界很好奇，所以还是开心+兴奋，回去路上就在车上睡着了。也不知道是不是这4000块钱的安全座椅太舒服了 🤣

\#3

因为下周三开始就是十一假期，所以这周日调休。

然后周末单休是真的累啊，感觉还没休息充分就结束了。尤其周日晚上还和 Lange 和 Hogan 上了一节 BC 团课

## Work

\#1

**C++ Weekly - Ep 499 - GCC's Stack Usage Analysis (and Warnings!)** https://www.youtube.com/watch?v=kXe-YkJ9nBs&t=105s

- 两个都是 gcc 的选项  `-fstack-usage` 生成函数栈使用报告；`-Wstack-usage=size` 对超出阈值的函数进行告警

**C++ Weekly - Ep 498 - Lifetimes of Local Variables** https://www.youtube.com/watch?v=F-gRrU-g22k

- 这一期感觉没啥新东西阿…

**C++ Weekly - Ep 263 - Virtual Inheritance: Probably Not What You Think It Is** https://www.youtube.com/watch?v=vZPkYvsqQxQ\

- 多继承中某个基类成员被重复的时候可以通过类似 D::I::m 来访问，不过虚继承这个时候一般会被用作消除成员重复的手法
- 如果 B 被 I1， I2， I3继承，并且 I2， I3 都是虚拟继承自B，这个时候编译器是能对I1没有虚继承告警

**C++ Weekly - Ep 261 - C++20's New consteval Keyword** https://www.youtube.com/watch?v=b22cKntJslU

- 介绍 consteval

**C++ Weekly - Ep 260 - C++'s Most Vexing Parse: How To Spot It, Why It Exists, and How To Avoid It** https://www.youtube.com/watch?v=ByKf_foSlXY

- 经典的 lexical parse 的问题

**The evolution of statements with initializers in C++** https://www.sandordargo.com/blog/2022/10/26/statements-with-initializers-part-1-conditionals

- 没啥东西，就是 if-init 和 switch-init

**What happens if my C++ exception handler itself raises an exception?** https://devblogs.microsoft.com/oldnewthing/20221021-00/?p=107307

- 应该算是常识性问题了…catch 后就不是 active exception，如果 raise exception 的时候已经有一个 active exception 就直接 terminate

**C++ 一个偶现的内存破坏** https://bot-man-jl.github.io/articles/?post=2022/Cpp-Memory-Occasionally-Corruption

- 其实没那么多道道，本质就是

    ```cpp
    class Response {
     public:
      std::string buffer() const { return buffer_; }
    ```

    这个函数返回类型就不应该用 std::string，直接创建了一个临时对象


**dynamic_cast 的实现方法分析以及性能优化** https://lancern.xyz/posts/2022/11/dynamic-cast-optimization

- 有很多 dynamic_cast 实现的细节，基本可以跳过，就知道一下结论：libstdc++/libsupc++的实现比 libc++ 高效，因为用了很多优化

**Lock-Free 班门弄斧(Hazard Pointer)** https://zhuanlan.zhihu.com/p/576073971

- 比较流水的讲了一下 hazard pointer 的几个点，但是看了一下没有正在 working on 的话这篇基本就是半废话了
- 先 mark 吧，后面那天研究 hazard pointers 的时候再回来看

\#2

这周修 Jenkins 的时候发现两三年没动的 vault-dev 这个服务突然就起不来了，几乎每次 jenkins 都挂着

发现本地可以复现之后赶紧看了一下 `sudo journalctl -fu vault-dev` 发现失败是一个很奇怪的错误码，然后把这个错误码发给了 GPT，GPT说可能是因为相关用户没有创建导致的，于是我把 .service 描述文件也塞给了 GPT 之后他更加确信是这个问题。

然后我运行了 `id vault` 发现真的没有这个 user，而且在我用 `adduser` 创建了用户之后这个服务就正常了。

这样更奇怪了，因为看 ansible 本身就没有创建服务的 steps 所以推测创建用户是安装的时候干的活，为啥突然现在就不安装了呢。

后来随便 run 了一下 `sudo dnf install vault` 我发现居然还提示我是否要安装 vault-dev ??? 那我前面安装的是啥？

赶紧 `dnf list --installed | grep vault` 看了一下发现之前安装的是一个叫 openbao-vault 的东西，这又是啥...

丢给 GPT 得知这是一个 EPEL 最近才提供的一个兼容 hashicorp vault 的东西。

然后想了一下大概知道了，因为我们用的 AlmaLinux 的 EPEL 实际上还是 streaming 的，所以最近也获得了 openbao-vault 的更新，并且这个东西是 preferred package，所以之前的命令就装上了这个版本。但是这个实现安装的时候并不会自动创建 user，而之前的 systemd 描述文件里又要求这个服务要运行在 vault 这个用户下，所以服务起不起来了。

解决方案是用 `--exclude` 参数去掉 openbao 这个

并且因为 ansible 的 dnf命令没法用这个参数（GPT说的），所以要改成 shell command

\#3

这周 fawkes 把 requset 部分都写好了，然后立马面临下一个坑，就是处理时遇到的异常怎么设计。

简单的说就是希望这个异常类可以保存 http status code（主要是 4xx 和 5xx），并且还能有一个 business layer error code，然后才是 error msg。

另外还希望业务层面如果主动抛出来的异常转换出来的 response 最好能是 JSON，例如

```json
{
    "error": {
        "code": 1234, // business layer error code
        "msg": "something went wrong"
    }
}
```

然后一时间困难症又犯了，目前大脑还在过度设计中 🫠

希望十一小长假期间可以有很多时间来写 fawkes

---

好了这周就这样，下周见
