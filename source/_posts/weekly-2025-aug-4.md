---
title: 一周杂记 in Week 4 Aug 2025
categories: CODE-LIFE
date: 2025-09-01 21:47:32
tags: [杂记]
---
本周（08/25 ~ 08/31）是8月份的第四周，也是最后一周，下周一正式进入九月份。

本周不仅一周平均天气炎热，还突发事情一茬又一茬

## Life

\#1

这周周一就迎来高温天气，加上周一又要开早会，所以索性高温居家了。

周二早上起来一看，豁，又是高温，不过想了想还是去办公室吧，毕竟在家里没人一起吃中饭啊。

到了办公室发现来办公室的同事好少，不过还好 suyang 也来了，中午起码有人一起吃饭。

周三略微降温，周四和周五又高温居家，只能说这周确实居家带的有点多了 🤣

\#2

周四居家还有一个目的，就是周四下午给娃预约了去社区医院打疫苗，顺带做个3个月的体检。

于是一下班就开车到了媳妇儿家，一起吃了中饭之后等娃那顿奶喂完，休息片刻就出发去社区医院了。

不过没料到因为马上开学，所以下午来补种疫苗的小学生特别多，想想可能以后还是白天来更好一些，这样娃打完疫苗代谢也能快一点。

体检也非常顺利，娃的状态都在正常值，不过三个月后的半岁体检说是要抽血，估计到时候会麻烦一些。

全部弄下来就花了不到50分钟，然后顺利开车回家，连停车费都不用付 🤣

不过当天娃还是有一些反应的，就是睡起来特别沉，而且明显有点嗜睡。

不过媳妇儿说是正常反应，而且第二天代谢差不多之后娃的整个状态也恢复到之前的情况了。

\#3

周六带上钱包和到手的民生银行信用卡，去山姆激活。

好不容易把车停好，买好东西，结账前去激活的时候发现，卧槽，我身份证居然没放在钱包里...

妈的这个只能怪我自己傻逼了...

于是周六晚上回家把身份证翻出来，周日又折腾了一遍终于才算完成...

回去的时候顺带买了一项黑松露火腿苏打饼干和寿司，这个饼干很合我胃口，手撕的话感觉一般，主要还是酱不如大渔的。

\#4

周五中午跑完步刚准备回家就接到我妈电话，让我赶紧给她晚上来杭州的票。

我一头雾水，问了一下才知道是丈母娘的婶婶过世了，她周日就要回老家，并且要在老家待几天。于是需要我妈过来帮忙带孩子。

周末算是加急训练带娃技能了

另外因为丈母娘周六傍晚要出去吃饭，老婆又想中午去印象城吃一席地，所以我原定周六中午跑完步之后去她家的计划就走不通了。

没办法，周五晚上又只能再跑6km，把本月90km的目标完成。

这一天上下跑了12km，是真的累...

\#5

周三和 iker 还有 chao 一起去新天地附近的影城看了死神来了6。

那个影院不得不说幕布是真的大，感觉真的是全杭州最大了。

- **死神来了：血脉诅咒 Final Destination: Bloodlines** 4/5 这不挺好嘛，也大概能猜到删减的部分都在哪，整体瑕不掩瑜。就是最后要是另一次提前预告然后进入下一个轮回没准更有意思 🤔


## Work

这周实在太忙了，这部分将就着看

\#1

**CppCon 2022 | "It's A Bug Hunt" - Armor Plate Your Unit Tests in Cpp - Dave Steffen** https://www.youtube.com/watch?v=P8qYIerTYA0&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=85

- 第一次看到从理论高度来指导如何编写 test cases 的
- accuracy 和 precision 那部分的辨析有点意思，另外中间用了异形2的电影片段来加深大家对于 accuracy 和 precision 不同的理解
- 整体结论就是
    - good tests are accurate and precise
    - accuracy: test results match reality
    - precision: test results are useful

**CppCon 2022 | Overcoming C++ Embedded Development Tooling Challenges - Marc Goodner** https://www.youtube.com/watch?v=8XRI_pWqvWg&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=90

- 微软提供的那套工具链（VSCode / VS / vcpkg）在嵌入式开发中的应用
- 不做嵌入式的随便看看就行，感觉问题不大

**Cancellations in Asio: a tale of coroutines and timeouts - Rubén Pérez Hidalgo** https://www.youtube.com/watch?v=80Zs0WbXAMY

- 果然还是新 talk 能紧追最新的内容，虽然是入门级别的 asio coroutine 指导，但是 cancellation 这部分真的不错
- 最近几个版本新增加了 `asio::cancel_after()` 可以方便针对某个 IO 做 cancel；还可以 (ab)use immediate co_await co_spawn 搭配 cancel_after 来对某一组操作做超时

**畅谈大型项目C++11->C++17/20升级** https://zhuanlan.zhihu.com/p/9671725199

- 别人项目升级编译器的过程参考
- 不过整体参考性并不多，大部分问题来自于新编译器更严格的检查模式；并且对于依赖库的使用和管理也不太一样

\#2

fawkes 稍微有了一些进度，大概想到了怎么封装 request/response，即这俩需要提供哪些。

目前是对着 Gin 和 Golang 自带的 net/http 来设计接口，求轻拍 🤷‍♂️

\#3

上周才把老板定的 P0 的 tasks 全都推进完毕，这周 SoS 会上老板直接对 P1 的定调说十月份前要做完...

没办法只能这样搞了。

这周挑了一个服务来改日志，上来就踩了 httpd 的深坑，然后换日志这个操作稍微借助了一下 Cursor。把各种规则讲清楚之后，Cursor 干一些重复度高的苦差事还是挺好使的。

然后这周还搞了一个“误会”：我在大群发了关于 Alerts on-call 的事情，美国那边的不知道怎么的都以为是那个新的 oncall process，觉得怎么没跟他们讲就搞了。。。弄得我还得第二天专门花了好久写了一个解释的 follow up message...

不说了，都是泪。

---

好了这周就这样，下周见
