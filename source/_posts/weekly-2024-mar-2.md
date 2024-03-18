---
title: 一周杂记 in Week 2 Mar 2024
categories: CODE-LIFE
date: 2024-03-18 22:25:38
tags: [杂记]
---

本周（03/11 ~ 03/18）是三月份的第二周，这周比较特殊，直接包含了新一周的周一

## Life

\#1

最近周围感染新冠（复阳）的同事慢慢又多了起来，组里好几个同事都一起中招了。

我自己的身体抵抗力还行，所以要么被免疫系统直接解决了，要么已经轻微的复阳过了，但是大体上对我没有造成明显的影响。

不过大家还是要注意身体啊

\#2

之前爷爷因为肺部感染晕倒进了医院，这周一开始就痊愈出院了。

本来以为可以这是可以庆祝的好消息，没想到直接得到了外公肠胃不适的坏消息。

而又过了几天，周五的时候接到妈妈的电话说外公这几天咳痰但是一直咳不出来已经连续五天没有进食了，我当场懵的一下。

问完媳妇儿之后立马回拨电话，说赶紧把外公送去医院，吸痰+输入营养液应该可以救回来。

本来以为这事儿也能平稳解决，结果周五晚上我打电话给我妈的时候我妈说和舅舅商量之后还是没送医院，因为已经错过最佳时间，下午外公就进入昏迷了...

媳妇儿提醒我说这种情况估计你外公挺不过第二天。我媳妇儿说话一直很直，但是我非常相信她作为医生的判断，所以我隐约觉得这个周末就再也见不到外公了。

果不其然第二天周六一大早妈妈给我打电话说外公过世了，家里已经定好了周一火化出殡，让我准备一下周日带着媳妇儿回趟老家。

周日到老家的时候已经是晚上8点，到外公外婆的房子的时候1F已经聚集大帮亲戚和教友（外公外婆是基督徒）在做祷告。赶车来不及吃晚饭，上楼吃了点东西后就和家人和教友一起做了告别和祷告，一直到十点多才结束。

走前陪着外婆说了一会儿话，试图缓解她的悲痛。外公外婆感情很好，我姐说外婆今天快独自哭了一天了。

接着就是周一一大早准备，带着外公遗体去火化，然后去墓园埋葬。

周一天还下着小雨，比起周日也是十多度的大降温，感觉格外的冷。

外公活了快94岁，之前身体也一直比较硬朗，直到两年前一次受伤感染导致一条腿做了截肢，至此只能长期卧床，生活状况也是急转直下。

所以也很难说这次对他来说是不是一种解脱，只希望他真的能 rest in peach

\#3

这部分就记一点琐碎的小事请把。

周四复出踢了一场球，虽然我自己踢的比较养生，但是对抗中还是给别人踢伤了，左脚脚踝直接踩破皮，右脚小腿直接被踢青...下周先歇一周养伤吧，这踢得实在有点狠了

一年前借了老爸50W现金，老爸拿了其中10W借给另外一个叔叔纾困，这周连本带利都换回来了，老爸继续收了本金，不过把1w4的利息转给我，也算一点小收益吧

周日上午和同事相约去浙影影院二刷了沙丘2，因为下午要收拾回老家处理外公的身后事，所以中午没和同事们一起吃饭，看完电影就速度回家了。

## Work

\#1

本周学习

**Design Patterns in Modern C++ | 9. Decorator**

- 传统的基于继承的 decorator pattern
- 利用 C++ template mixin 的 decorator
- 利用 模板传递函数或者使用 std:function 的高阶函数版 decorator

**Design Patterns in Modern C++ | 10. Facade**

- 讲的都是啥呀这都是……

**Design Patterns in Modern C++ | 11. Flyweight**

- flyweight 就是为了节省内存内部服用具体对象，这个对象通过一个 token 暴露给使用者
- boost 自带了一个 flyweight util

**Design Patterns in Modern C++ | 12. Proxy**

- 就是单纯的 proxy…

**CppCon 2021 | Back to Basics: Templates (part 1 of 2) - Bob Steagall**

- C++ template 基础
- 感觉这老爷爷讲的真不错，说话铿锵有力

**CppCon 2021 | Back to Basics: Templates (part 2 of 2) - Bob Steagall**

- 这个 part two 满满都是基础干货，还覆盖了 full specialization + partial specialization 以及 partial specialization 为基础可以实现 type traits
- 这两节课差不多就是从其它语言转过来的人的 must watch videos 了

**You Cannot Have Exactly-Once Delivery** https://bravenewgeek.com/you-cannot-have-exactly-once-delivery/

- 现在算是老生常谈了，分布式系统根本不存在原生的 exactly-once
- 实际上大家用的最多的就是 at least once，不考虑重复，如果需要避免就做成幂等或者引入 dedup
- at most once 也行，但是为了做到 once 多个 worker 下还是需要一些全局同步机制

**Distributed Systems Are a UX Problem** https://bravenewgeek.com/distributed-systems-are-a-ux-problem/

- 这篇有点像是作者的吐槽了，文章提到分布式系统错误是常态，有时候应该以UX的角度去解决，回到传统行业如何解决一些固有问题的手法
- 即：Compensation has a lot of applications as a UX principle because it’s really the only way to build loosely coupled, highly available services.
- 然后就是该道歉就道歉，这玩意儿比想象中的好用…

\#2

这周花了点时间终于把 nlohmann-json serde 给写完了，花费了无数的脑细胞...

PR：https://github.com/kingsamchen/Eureka/pull/20

后面稍微改改，比如调整一下 base64_encoded 这个 opt 的实现应该就可以直接用到在公司的项目里了

\#3

之前好几次遇到 vagrant/virtualbox 在 Windows 上启动时候卡在 ssh setup 上

后来想到可能是因为默认用的 2222 端口有时候在 Windows 上会被系统保留导致的

于是调整了一下 Vagrantfile，选用一个不会被保留的端口号作为 vagrant ssh 端口，并且禁用掉默认的 2222 端口。

PS：这里显式禁用是必须的，否则 vagrant 自己会尝试使用 2222

```ruby
  config.vm.network "forwarded_port", guest: 22, host: 2222, id: "ssh", disabled: "true"
  config.vm.network "forwarded_port", guest: 22, host: 10022
```

reload 之后再也没出现过卡 ssh 的情况了

---

好了本周就这样，下周见
