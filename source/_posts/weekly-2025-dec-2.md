---
title: 一周杂记 in Week 2 Dec 2025
categories: CODE-LIFE
date: 2025-12-15 20:52:37
tags: [杂记]
---
这周（12/8 ~ 12/14）是12月第二周，这周偏冷啊。

## Life

\#1

这周状态有点紧绷，连续好几天夜间睡觉的 HRV 都掉到了很低的触发红色警戒的值，也不知道什么情况

问了 GPT 说是哪怕睡觉时候大脑也没有处于放松状态，而是时刻准备逃跑/战斗的危机状态

这就导致虽然睡眠时间看起来正常，但是实际上大脑和心脏都没有获得整整的休息，白天 body battery 非常低，很容易疲劳

而且每日平均心率比之前突然升高好多，身体也一直处于紧绷的状态

下周估计要针对性的做一点调整了，比如早点上床睡觉，入睡前多放松；以及日常多做一下深呼吸的操作

## Work

\#1

**CppNow 2025 | Safer C++ at Scale with Static Analysis - Yitzhak Mandelbaum** https://www.youtube.com/watch?v=3zQ4zw4GNV0&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=42

- 分享了一下 google 内部对于这部分的经验
- 内部大量使用 notnull/nullable annoation 来标记 raw pointer 的接口（有点好奇怎么有这么多）
- 另外他们已经开始尝试使用 clang/libc++ 的 std hardening，就是一些常见操作会有检查，代价是大约 0.3% 的性能开销
- 然后是强制初始化
- 另外还提到了 clang 的一些 lifetime 的实验性功能，不过目前停留在function scope

**CppNow 2025 | Harnessing constexpr - A Path to Safer C++ - Mikhail Svetkin** https://www.youtube.com/watch?v=THkLvIVg7Q8&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=41

- 利用 constexpr 来避免 UB 和提前发现错误
- 缺点是适用场景覆盖有限而且对标准的要求高（忘了23还是26所有的 containers 才都 constexpr-化）

**C++ Weekly - Ep 509 - Can Lambdas Inherit Interfaces?** https://www.youtube.com/watch?v=f0heIju3udc

- 很有意思的一个分享，lambda 实际上就是一个 callable object 那么自然也可以直接继承，但是以往的时候这个也没有什么实际作用
- 但是随着 C++ 23 提供了 deducing this，就可以把 lambda 的参数变成 auto&& this self，然后把具体实现点放到子类中，基本就是 CRTP 的做法
- 不过感觉真有需求还是先考虑正道比较好

**C++ Weekly - Ep 226 - The Arrow Operator Is Magic** https://www.youtube.com/watch?v=mAHHKDyLmCI

- 核心就是：重载 `operator->()` 之后的 usage 其实是需要编译器配合的，而且这个是 since day one 就写在标注中

**Moving From Intel to ARM - Apple's Big Performance Mistake?** https://www.youtube.com/watch?v=JAr0c6le_F8

- 上历史课 + x86/64 和 ARM 汇编的简单对比

**C++ Weekly - Ep 225 - Understanding Operator Overloading** https://www.youtube.com/watch?v=gjFrjNK3Dq4

- 科普 operator overload，不过没有提 hidden friend 优势有点可惜

**C++ Weekly - Ep 224 - RPG In C++20 - Part 5: Dealing With Game Input Events** https://www.youtube.com/watch?v=EF-lO3daS7I

- 直播写代码 part 5

**asio c++20协程的 redirect_error 用法改善** https://www.jackarain.org/2022/09/18/asio-redirect_error_improve.html

- 思路不错，继承后加一个 operator[] 重载

    ```cpp
    namespace asio_util
    {
     template <typename Executor = boost::asio::any_io_executor>
     struct asio_use_awaitable_t : public boost::asio::use_awaitable_t<Executor>
     {
      constexpr asio_use_awaitable_t()
       : boost::asio::use_awaitable_t<Executor>()
       {}

      constexpr asio_use_awaitable_t(const char* file_name,
       int line, const char* function_name)
       : boost::asio::use_awaitable_t<Executor>(file_name, line, function_name)
       {}

      inline boost::asio::redirect_error_t<
       typename boost::decay<decltype(boost::asio::use_awaitable_t<Executor>())>::type>
       operator[](boost::system::error_code& ec) const noexcept
       {
        return boost::asio::redirect_error(boost::asio::use_awaitable_t<Executor>(), ec);
       }
      };
    }
    ```

- 不过现在必要性不大了，因为 ASIO 好早之前的版本就把默认的 completion_token 换成了 deferred，而 deferred 可以直接无缝衔接 co_await，所以现在直接用 redirect_error 就行

    ```cpp
    std::error_code ec;
    co_await repeat.async_wait(asio::redirect_error(ec));
    ```


**asio异步化的实现方法** https://www.jackarain.org/2023/03/01/asio-how-to-async-for-me.html

- 对应 asio official examples 中的 Operations 那一部分
- 不过官方例子都没有涉及到切换到某个线程执行的过程，所以一直没注意到 asio 提供的 completion token adapter，例如 deferred, use_awaitable 这些都是 non-copyable 的，只能 move 到 lambda 里；考虑到 token 的 signature 都是 auto&&，所以 callback 实现上最好至少要能支持 moveable

**浅谈 std::wstring_convert 及 utf 编码转换** https://www.jackarain.org/2023/04/30/wstring_convert.html

- 然而 codecvt 那几个都已经被标记成 deprecated 并且 removed in 26 了 https://en.cppreference.com/w/cpp/header/codecvt.html
- 以后应该是 26 那个 text encoding proposal 了

**协议实现不应该关心字节序** https://www.jackarain.org/2023/05/16/byte-order-not-matter.html

- 我觉得还是没法不关心的，文中的例子也是选取一种比较直观的方式作为目标字节序

    ```cpp
    template<typename type, typename source>
    type read(source& p)
    {
    	type ret = 0;
    	for (std::size_t i = 0; i < sizeof(type); i++)
    		ret = (ret << 8) | (static_cast<unsigned char>(*p++));
    	return ret;
    }

    template<typename type, typename target>
    void write(type v, target& p)
    {
    	for (auto i = (int)sizeof(type) - 1; i >= 0; i--, p++)
    		*p = static_cast<unsigned char>((v >> (i * 8)) & 0xff);
    }
    ```

    这里本质上也是转成 big-endian

- 不过做数据传输的时候采用更 standard/general 的协议这话是没错的，比如 pb for binary，json 甚至 xml for text

**现代 c++ 解决 c++ abi 问题的一些方法** https://www.jackarain.org/2023/06/10/modern-cpp-abi.html

- 利用 inline namespace 注入版本号到符号名中，这样 lib 更新（并更改版本号后）user 强制需要重新连接
- 严格来说并没有解决 ABI 问题，只是提供了一个途径避免误用

\#2

这周一个大进度是把 fawkes servier side cookie support 给做完了

PR 见：https://github.com/kingsamchen/fawkes/pull/15

发现时间还有多，顺带调整了一下 fawkes target 的 naming convention https://github.com/kingsamchen/fawkes/pull/16；主要是觉得 `fawkest.tests` 这种实在有点奇怪。

这次修改不小心“恢复”了之前 pch target 的 ALIAS target `fawkes::pch`，但是神奇的发现 reuse target 居然在 Windows 上可以编译通过了；以前 MSVC 会试图创建包含 `:` 的目录而导致失败，这次观察了一下构建过程，发现不会再创建类似目录了。

我一开始以为是 MSVC 更新后解决的，但是跑了 Github CI 后发现 CI 还是会失败，后来想到可能是 CMake 升级后解决的，于是改了一下 Github Windows CI，使用最新的 CMake 之后发现确实没有这个问题了...

这算是个好事吧 🤔

\#3

这周遇到一个 clang-tidy 的有点坑的地方。

fawkes cookie support 部分用到了 beast 的 http request_headers，底下实际上是 boost intrusive linked list，但是这个容器的 iterator clang-tidy 不认识，函数参数 pass-by-value 时就会 complain。

折腾了好久怎么 suppress 最后发现正确做法应该是这样：

- 在需要的地方 `using iterator = boost::intrusive::concret_iterator;`
- 然后把 iterator 加到 `performance-unnecessary-value-param.AllowedTypes` 里

---

好了这周就这样，下周见
