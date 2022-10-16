---
title: 一周杂记 in Week 3 Oct 2022
categories: CODE-LIFE
date: 2022-10-16 23:38:40
tags: [杂记]
---

本周是十月第三周，也是十一假期后全面恢复上班的第一周。

## Life

\#1

这周五终于和老婆在小区某幢楼下的球桌上打上了乒乓球 🏓

十分过瘾和满足

下周老婆从富阳回来休息大概是周三、四、五，希望能打上两天（四五？）

\#2

这周四踢了场球，上半场发挥还行，下半场就有点惨了。

球场的碎胶粒掉进了鞋子，把左脚大拇指下方的脚底板直接给磨破皮出血了...

下周打算先歇一周回复一下，然后考虑是不是真的得买一双封闭性好点的鞋子...

\#3

这周看了两部电影：《独行月球》 和 《Kingsman 起源》

这两部给我的感觉差不多，都是中规中矩。

甚至独行月球给我的感觉比后者更好。

虽然老婆觉得独行月球搞笑的地方太少了，比不上夏洛特烦恼和羞羞的铁拳，但是我个人还算是觉得这影片及格往上还是有的，大概 6.5/10吧

Kingsman 的话剧本硬伤有点多了，这么强大的 Cast 最终的呈现其实是让人略微失望的， 5.5/10

## Work

\#1

这周工作重点是配合其他同事一起把我们 codebase 中的 Status 从 protobuf/stubs 切换到 absl

感慨一句终于切了啊，虽然是被某个库的升级逼着切换的。

讲道理要是半年前听我的意见 gradually phase out 的话，这周就不会这么鸡飞狗跳了。

因为几乎每一个源码文件都会涉及，并且我们对 Status 用法千奇百怪，手动一个一个替换几乎不可能，所以我写了一个 python 脚本来处理大多数的情况。

后来 suyang 在这个基础上又做了一次扩展，使得脚本几乎能处理所有我们已知的 case。

不过验证编译和测试的时间还是花了不少

脚本我贴这了：https://gist.github.com/kingsamchen/10a8a3e798eb59f27e0335a087134aca

检查了一下也没有泄露业务信息，还好

\#2

本周没有继续学习 absl/synchronization，下周再弄吧...

\#3

本周学习进度：

- CppCon 2020 | **How C++20 Changes the Way We Write Code - Timur Doumler**
  介绍了 big-4 features: Coroutine, Concept, Ranges and Modules
  其中 coroutine 着墨最多，不过最后还是建议大家用 3rd lib，不要自己趟这个浑水。
  （这部分学到了 promise handle 是一个 stack 对象，关联一个 heap 分配的 coroutine frame，并且在很多情况下编译器可以优化掉这个 heap allocation）
- CppCon 2020 | **Halide: A Language for Fast, Portable Computation on Images and Tensors - Alex Reinking**
  太高端了，而且感觉学不到啥，所以跳过了
- 网络编程实战
  继续学习，目前感觉还不错；虽然目前的内容都是基础，但是可以作为查漏补缺
  主要是知识点展开合理，没有东边一下西边一下
- 两篇 posts：
  - [Knowing when not to use the STL algorithms - set operations](https://cukic.co/2018/06/03/set-intersection-in-cxx/?utm_source=pocket_mylist)
  核心大意就是根据具体的情况选择使用的 tools, 不要 blindly use STL algorithms
  我觉得是废话…
  C++ rtti vs 宏 - 如何优雅的获取类型T的name或ID
  - [C++ rtti vs 宏 - 如何优雅的获取类型T的name或ID](https://zhuanlan.zhihu.com/p/360482391)
    主流编译器都支持 `__FUNCSIG__` 或者 `__PRETTY_FUNCTION__` 宏，这个宏表示当前函数的签名，并且宏自然是编译期
    利用一个函数模板就可以把目标类型 T 挂上去

    ```cpp
    template<typename T>
    constexpr const char* type_name_raw() {
        return __FUNCSIG__;
    }

    template<typename T>
    constexpr std::string_view type_name() {
        std::string_view raw = type_name_raw<T>();
        std::string_view prefix = "const char *__cdecl type_name_raw<";
        std::string_view suffix = ">(void)";
        raw.remove_prefix(prefix.size());
        raw.remove_suffix(suffix.size());
        return raw;
    }

    int main() {
        using vs_t = std::vector<std::string>;
        constexpr std::string_view t = type_name_raw<vs_t>();
        std::cout << t << "\n\n" << type_name<vs_t>();

        return 0;
    }
    ```

    `std::string_view` 支持 constexpr，再利用编译期 std::string_view 操作分理出 T 即可

---

本周就是这样，下周见
