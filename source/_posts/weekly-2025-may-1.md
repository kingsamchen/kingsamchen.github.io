---
title: 一周杂记 in Week 1 May 2025
categories: CODE-LIFE
date: 2025-05-05 11:30:42
tags: [杂记]
---
本周（04/28 ~ 05/04）是五月的第一周，周四是五一 labor day，终于进入假期了。

## Life

\#1

周三请了一天假，陪同媳妇儿去浙二参加博士复试。

因为市中心交通不好而且打听了一下浙二停车是个头疼的事情，所以直接选择打车去。

至于结果嘛，只能说有点失望，但是意料之内，没有录取。

因为没有找关系，所以很难 compete 那些相同甚至条件更差的人。休息厅坐我和媳妇儿前面有个女生一副胸有成竹的样子，结果还没出来就请周围的人喝咖啡和面包，但是一开始媳妇儿觉得她一定会被刷掉，因为她是省中医院放射科的，不可能会有心内的导师会选她。结果呢，最后发现浙二院长王建安直接选了她...

而媳妇儿目标导师，另外一家医院的大主任，早在去年联系的时候就说自己名额满了，当时还不信邪，依然填报了。过了初试的时候复试名单找了个熟人问了一下说这个主任自己的学生不在里面，当时还很高兴，觉得机会很大。最后发现人家选了一个浙大口腔的...口腔甚至不是临床，医师执照都不一样。而且这哥们当时就坐我边上，面试完就走了，被我下楼买面包的时候碰到了 🤡

又因为干了好几年临床，所以科研论文也 compete 不过那些应届生，这个就不用多说了。我媳妇儿发的SCI才2分，那天面试的应届生好几个硕士时候发的论文都是 6-8 分的，摊手

不过没录取也算个好事，就不折腾博士了，反正现在国内医学院博士和临床也没关系，基本都是得进实验室当牛马

\#2

岳父五一也来杭州了，参观了一下我在城西这边的房子，然后一起吃了个晚饭。之后开车送岳父母回媳妇儿房子

5.3 的时候带着媳妇儿开车去她家和岳父母一起吃了个中饭。

不过因为当天有点热，媳妇儿不太愿意跑到外面去，而且挺个大肚子确实也不方便，所以吃完饭之后就在家里呆着了。

讲道理确实挺无聊的 🫠

我刷了一会儿手机实在受不了了，打开笔记本开始写代码。然后还找到正在写的 http-router 的一个 bug。

不过因为媳妇儿家没有办公桌（书房基本没有搞，现在给当成杂物间了），所以沙发上干活还挺累的。

晚上吃完饭之后和媳妇儿还有岳母下楼转了一下，就开车回家了

\#3

五一期间断断续续终于把 mad max: furiosa 看完了

这几天看了一下口碑，突然想看**雷霆特工队\***，约了 hogan 用了观影折扣券去了水晶城看。有车确实还挺方便，水晶城这天停车也方便，就是这个mall确实人流不太行

- **疯狂的麦克斯：狂暴女神 Furiosa: A Mad Max Saga** 3/5 太一般了，anya 感觉都没入戏，锤哥反而有点过猛把军阀演成了哔哔赖赖的痞子，人物塑造失败就算了，战役啥的也没拍出来，几个镜头切一下就完了...我严重怀疑锤哥接这戏是为了给他老婆安排一个角色
- **雷霆特攻队\* Thunderbolts\*** 4/5 有种滚导当年拍银护的感觉，但是多角色交互和角色弧的挖掘确实没有银护做的好。不过能找A24班底来搞起码说明对上一阶段的粗制滥造是有反思的，希望后续的片子别再拉跨


## Work

\#1

**CppCon 2022 | Aliasing in C++ - Risks, Opportunities and Techniques - Roi Barkan** https://www.youtube.com/watch?v=zHkmk1Y-gqM&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=110

- 有些场合要注意是否会出现 aliasing 的问题，并且编译器为了避免这类问题，有时候生成的代码会比较保守
- 介绍了一些避免 aliasing 的方法，其中采用 type alias 的那个有点意思
- 不过如果不是 performance critical 的逻辑，因为潜在 aliasing 导致的劣化估计也不太重要

**CppCon 2022 | REUPLOAD: The Hidden Performance Price of C++ Virtual Functions - Ivica Bogosavljevic** https://www.youtube.com/watch?v=kRdbqjw2WIs&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=112

- 和 https://kingsamchen.github.io/2024/11/24/weekly-2024-nov-3/ 中提到的 **[MUC++] Ivica Bogosavljevic - The performance price of virtual functions in C++** 其实是一个话题…那边的结论也完全使用这个

**C++ Weekly - Ep 477 - What is Empty Base Optimization? (EBO)?** https://www.youtube.com/watch?v=KU-Ahb86izo

- 介绍的 EBO 和 EBCO；C++ 20开始可以用 `[[no_unique_address]]` 这个 attribute 来更好地表达
- 需要注意的是，MSVC 因为 ABI 的原因，EBCO 是没法直接利用的，需要用 `__declspec(empty)` 类似的 MSVC下 no_unique_address 这个 attribute 几乎是个 no-op

**C++ Weekly - Ep 354 - Can AI And ChatGPT Replace C++ Programmers?** https://www.youtube.com/watch?v=TIDA6pvjEE0

- 单纯展示一下 chatgpt 的一些使用可能？
- 现在这个节点看应该算是日常了；未来一些 trivial 的活都可以 AI powered 了 LOL

**C++ Weekly - Ep 353 - Implicit Conversions Are Evil** https://www.youtube.com/watch?v=T97QJ0KBaBU

- implicit conversion 会不经意间导致的各种坑
- 所以有条件一定要打开 clang-tidy 的 implicit conversion 相关的 rules

**C++ Weekly - Ep 352 - Not Doing This Should Be Illegal! (Always Fuzz Your C++!)** https://www.youtube.com/watch?v=Is1MurHeZvg

- 演示如何使用 llvm 的 fuzzy testing，现在这块用户上手门槛已经做得很低了
- 在挂接函数里测试你的目标函数

    ```cpp
    extern "C" int LLVMFuzzerTestOneInput(const uint8_t *Data, size_t Size) {
      DoSomethingWithData(Data, Size);
      return 0;
    }
    ```

    然后编译连接时候 `-fsanitize=address,fuzzer` 就行

- 更详细的 doc 可以看 https://llvm.org/docs/LibFuzzer.html 和https://github.com/google/fuzzing/blob/master/tutorial/libFuzzerTutorial.md

**C++ Weekly - Ep 351 - Your 5 Step Plan For Deeper C++ Knowledge** https://www.youtube.com/watch?v=287_oG4CNMc

- 一些可以用来学习&深入研究C++行为的 tips/tricks

**C++ Weekly - Ep 478 - Lambdas on the Heap (2)** https://www.youtube.com/watch?v=qMGi_tdKrrk

- 继续之前的整活系列，然后学到了 auto ptr = new auto {value}; 这个用法
- 不过没事真的别用 🤡

**Polymorphic, Defaulted Equality** https://brevzin.github.io/c++/2025/03/12/polymorphic-equals/

- 整篇 blog 的核心是，Derived class 的 default operator== 实现除了 member-wise comparison 之外还会比一下 sub-object，即实际上生成的是

    ```cpp
    auto Derived::operator==(Derived const& rhs) const -> bool {
        return static_cast<Base const&>(*this) == static_cast<Base const&>(rhs)
           and m1 == rhs.m1
           and m2 == rhs.m2;
    }
    ```

- 另外我觉得这个 comparison 的语义设计一开始就很奇怪，看起来就是个 bad design

\#2

这周把 C++ 版本的 http-router 的 add_route 部分给写完了，并且把 golang 版本的 test cases 也一并加到了测试集里。

跑某个 test case 的时候失败了，那会儿刚好在媳妇儿家。因为没有办公桌，所以直接坐在沙发上用笔记本开着 vscode 的 msft-cpptools 插件的调试，单步追踪了一下，还好这个问题比较简单，比较容易就找到了 root cause。

不过确实犯了一个低级错误，而且之前的一些 tests 居然没有发现有点离谱。

另外开了 address sanitizer 的二进制是没法直接挂调试器调试的，感觉这个有点蛋疼。

后续就是把 locate path & extract path variables 这部分给做好，基本 http-router 就完成一半了（大概吧）。

---

好了这周就这样，下周见
