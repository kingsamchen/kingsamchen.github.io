---
title: (Ab)use system_error exception for System Calls
categories: PROGRAMMING
date: 2021-10-17 21:31:34
tags: [cpp, exception]
---
C++ 11 引入了没有掀起什么波澜的 [std::error_code](https://en.cppreference.com/w/cpp/error/error_code) 和几乎没收到什么关注的 [std::system_error](https://en.cppreference.com/w/cpp/error/system_error)。

偶然发现后者可以用来做针对 system calls 的异常汇报处理。

### 0x0 A Traditional Approach

Win32 API 调用失败后具体的错误码可以通过 `::GetLastError()` 获取；Linux 上 syscall 的错误码则用更 C-ish 的 `errno` 获取。

如果希望通过异常来汇报这些系统调用失败，那么通常会从 `std::runtime_error` 继承出一个 exception class，额外包含系统调用的错误码。

例如：

```c++
class win_last_error : public std::runtime_error {
public:
    using error_code_type = unsigned long;

    explicit win_last_error(const char* msg)
        : runtime_error(msg),
          error_code_(::GetLastError()) {}

    [[nodiscard]] error_code_type error_code() const noexcept {
        return error_code_;
    }

private:
    error_code_type error_code_;
};
```

上面的代码稍加处理后可以同时支持 `GetLastError()` 和 `errno`

### 0x1 Using std::system_error

`std::system_error` 恰好也是一个和 system facilities 有关的异常类，汇报系统相关的错误。

除了传统的 `what()` 之外它还提供一个 `code()` 函数返回对应的 `error_code`

而 `std::error_code` 可以通过指定不同的 category 来指示其包含的错误码语义。

所以实际上我们可以这么用：

```c++
inline pipe make_pipe() {
#if defined(OS_WIN)
    ...
    if (::CreatePipe(&fds[0], &fds[1], &sa, 0) == 0) {
        throw std::system_error(std::error_code(::GetLastError(), std::system_category()),
                                "CreatePipe() failure");
    }
    ...
#else
    ...
    if (::pipe(fds) != 0) {
        throw std::system_error(std::error_code(errno, std::system_category()),
                                "pipe() failure");
    }
    ...
#endif
}
```

注意这里使用的 `system_category()`，它代表 error code 是 reported by the system.

---

有些地方可能会说这里要使用 `std::generic_category`；但是通过试验发现使用 `std::generic_category` 的 Windows last-error 和 `ec.message()` 是对不上的；而 `std::system_category()` 则是正确的。

猜测 generic category 可能针对的是 CRT 的错误码

### 0x2 Q&A

**Q: error_code 是啥？**
A: 这玩意儿实际上是 ASIO 的作者针对 async-handler 的错误处理设计的；boost 中 ASIO 和 filesystem 都大量支持了它。

但是这玩意儿的设计门槛有点高，以至于不看完几篇文章压根没法理解这东西的设计意图；导致除了 ASIO 和 filesystem 及他们相关的衍生 lib 之外都没啥人去用它。

而 filesystem C++ 17 才进入标准；而 ASIO/network TS 几天前也差不多宣告死亡了...

**Q: 为啥要用异常？**
A: Why not？我不排斥使用异常，而且我觉得我写的东西还没关键到需要考虑目前异常实现带来的性能问题。

并且我觉得 exception 在充满大量业务逻辑的应用层是非常优秀的错误处理机制。

即使提供了支持 chaining operations 的 sum type（例如这个 [expected](https://github.com/TartanLlama/expected)），在业务层基本也需要人肉实现 failure cascading 层层上抛。

<!-- more -->
