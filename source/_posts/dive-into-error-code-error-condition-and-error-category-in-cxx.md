---
title: Dive into error_code, error_condition and error_category in C++
categories: PROGRAMMING
date: 2024-02-25 14:49:19
tags: [cpp, error handling]
toc: true
---
最近抽了点时间，重新读了一下 Christopher Kohlhoff 早年写的几篇介绍 `std::error_code` 由来的文章（后面简称“系列文章”），以及 `Boost.System` 这个 lib 的相关文档（后面简称 Boost Doc)。

并且为了确保自己理解了这些内容，以 http status code 为基础实现了可以根据处理场景扩展的错误码体系。

## Why yet-another error-code family

为什么不用异常二要引入一套全新的错误码系列，系列文章开头讲了很多理由，但是在我看来直切要害的就两条：

1. 在以 callback handler 为主要手段的异步逻辑处理中，传递错误码比起传递异常要轻松且自然的许多，因为很可能发生错误和处理错误不在同一个线程
别忘了 exception ptr 也是直到 C++ 11 才被正式引入，并且使用门槛更高
2. 不管出于何种原因，有些开发领域会默认禁用异常

## Goals

深入探究每个具体类型前，最好先了解一下这套体系的目的，看看它想解决的问题。

系列文章给出的目的可以归结为三点：

1. 统一 Windows 和 POSIX 平台上的错误码机制，尤其是处理错误这块
2. 能够支持保存最原始的错误码，用以查错支持
3. 允许用户根据自身需求实现扩展

其中前两点针对的是调用 C lib 函数以及平台提供的函数调用，例如 Win32 API。因为 ASIO 是一个IO事件框架网络库，需要密切地和系统打交道。

对于 POSIX 系统来说，不管是 C lib 函数还是 syscall，基本都是通过：

- 函数返回非 0
- 设置 `errno` 为具体错误码，例如 `EINVAL`

来做错误汇报。

而 Windows 上就复杂许多：

- msvcrt 提供的 C lib 函数也会使用 `errno`，并且出于兼容性，msvcrt 提供了大部分 POSIX `errno` 的[定义](https://learn.microsoft.com/en-us/cpp/c-runtime-library/errno-constants)，但是相当一部分错误码不会被 Windows 使用
- Win32 API 的具体错误码需要通过 `GetLastError()` 获取
- 网络 socket 相关的函数，不管是 BSD/socket compliant 的函数，还是 `WSA-` 打头的专有函数，都得使用 `WSAGetLastError()` [获取](https://learn.microsoft.com/en-us/windows/win32/winsock/windows-sockets-error-codes-2)

我要是 Christopher 我也能给烦死…

至于第三点也是理所当然，如果你这套错误码体系用户自身的代码逻辑还不能使用，那还不如回退到最原始的 int 错误码时代呢。

并且设计上最好保留足够的灵活性，让用户代码**用**起来特别舒服。

吐槽一句，在使用体验上，我觉得 abseil 的 `Status`/`StatusOr` 做的是真的挺差的 🤣

## std::error_code

`std::error_code` 用 0/unset 来表示成功，这个属于沿袭传统了。并且定义了 `operator bool` 所以使用上可以

```cpp
std::error_code ec;
// use with ec
if (ec) {
  // failure
}
```

另外 `std::error_code` 保存的是调用返回的**原始值**，所以用 `ec.value()` 获取的具体值**可能**是平台相关的。

以系列文章中的例子来说：调用 `create_directory()` 如果因为文件夹已经存在而失败，在 POSIX 上这个值是 `EEXIST`，但是在 Windows 上则是 `ERROR_ALREADY_EXISTS`，它们的具体数值是不一样的。

所以可以得出推论：如果需要针对特定错误进行处理，并且这个错误来源是**平台调用**，那么使用 `ec.value()` 有很高的概率是用错了。

💡 注意到上文加粗的“可能”二字，后面会介绍例外情况

一般来说原始值的使用场景就是写日志这种 report error 的场合，用以后续辅助 trouble shooting。

## std::error_condition

`std::error_condition` 的设计目的就是用作：处理某种错误情况（*error condition*)

某种错误情况其实是一个更高层次的抽象概念，也应证了计算机科学中创建一个中间层来解决问题的思路。

我们可以抽象出一个 `file_exists` 的 error condition，既能表示 POSIX 上的 `EEXIST`，也能表示 Windows 上对应 `ERROR_ALREADY_EXISTS`。

这样一来我们就能处理这种特定的错误情况了：

```cpp
std::error_code ec;
create_directory("/some/path", ec);
if (ec == file_exists) {
    // handle already exists
}
```

更一般的，我们甚至可以抽象出一个 `low_system_resource` 的 error condition，既能表示系统内存资源不足，也能表示系统磁盘不足；即我们将多个基本的 error code/case 关联到一个 error condition 上。

所以如何定义 error condition，其实着重考虑的是：在我们的业务场景下，需要从哪些情况来处理这些错误；侧重点是**如何处理错误**。

系列文章中就 error condition 的抽象意义还举了一个例子：假设你是一个 database 的作者，你需要提供一些概念上的 error codes 让用户针对性处理。对于“表不存在”这种错误，从设计层面上看暴露给用户 table_not_exist 肯定是要好过直接暴露 no_such_file_or_directory 甚至于 ENOENT 这种无意义暴露内部细节的。

### std::errc

C++ 标准库定义了一个 `enum class std::errc`，每个 entry 都对应（能自动转换为 `std::error_condition`）一个 error condition。

事实上，这个 [errc](https://en.cppreference.com/w/cpp/error/errc) 是根据 POSIX errno list 几乎 1:1 建模的，所以能覆盖几乎所有系统平台相关的错误情况。

`std::error_condition::value()` 返回的就是 errc 中每个 entry 的 raw enum value；类似的，non-zero 表示错误情况

## std::error_category

不管是 `std::error_code` 还是 `std::error_condition` ，同一个数值在不同的场合下代表的意义都是不一样的。error category 决定了这个 error code 或 error condition 应该如何被解释。

`std::error_category` 是抽象基类，需要根据需求实现具体派生 category。

标准库提供了几个预置的 error category，重点是 `std::system_category` 和 `std::generic_category`。

在 POSIX 系统上这俩没有本质区别，而在 Windows 上，前者直接对应 non-msvcrt 那部分错误上下文（例如 Win32 API），后者针对 msvcrt 的错误上下文。

error category 的解释功能由两部分组成：

- 提供 `default_error_condition()` 支持将一个 `std::error_code` 转换为对应的 `std::error_condition`
system category 重写了这个函数，将能匹配 POSIX errno 的原始错误码映射到对应 POSIX errno 上并且封装进 generic category 的 error condition 中。
这个函数会被自动应用于 `std::error_category::equivalent` 的下面这个重载的默认实现

    ```cpp
    // Default behavior: default_error_condition(code) == condition.
    virtual bool equivalent(int code,
                            const std::error_condition& condition) const noexcept;
    ```

- 重写另外一个 equivalent 重载

    ```cpp
    // Default behavior: *this == code.category() && code.value() == condition
    virtual bool equivalent(const std::error_code& code,
                            int condition) const noexcept;
    ```


自己定义 error category 时这两部分（三个虚函数）的重写都是可选的；如果不做任何调整那么转换前后都没有变化。

在语义上，error category 的这个解释功能的本质是：定义一个 error code 和一个 error condition 是否等价

💡 C++ 中 equivalence 和 equals 是具有明显语义区别的

`std::error_condition::operator==` (operator!= 类似) 具有如下[重载](https://en.cppreference.com/w/cpp/error/error_condition/operator_cmp)

```cpp
// true if either code.category().equivalent(code.value(), cond) or cond.category().equivalent(code, cond.value())
bool operator==( const std::error_code& code,
                 const std::error_condition& cond ) noexcept;
bool operator==( const std::error_condition& cond,
                 const std::error_code& code ) noexcept;
```

这也是前面 error code 能和从 errc 自动构建的 error condition 比较等价的基础

## Practice：Creating an error code

再继续深入前我们先了解一下如何在自己代码中调用 c libs 或平台调用后创建对应的 `std::error_code` 能够加深我们的理解。

首先要明确一点，在我们的代码中，肯定是已经拿到了错误码，不管是 `errno` 还是 `GetLastError()` 然后需要将他封装进 error code；并且这个时候我们是不应该需要知道具体的错误码的值。

所以我们创建 error code 需要通过 `std::error_code` 的构造函数来进行

```cpp
// Windows

// call c libs
auto fp = std::fopen(path, "rb");
if (!fp) {
    return std::error_code(errno, std::generic_category());
}

// call Win32 API
if (::CreatePipe(&fds[0], &fds[1], &sa, 0) == 0) {
    return std::error_code(::GetLastError(), std::system_category());
}

// POSIX

// call c libs
auto fp = std::fopen(path, "rb");
if (!fp) {
    return std::error_code(errno, std::generic_category());
}

// call syscall
// in terms of impl, you can use std::generic_category() too, but system category is more semantic
// compliant
if (::pipe(fds) != 0) {
    return std::error_code(errno, std::system_category());
}
```

因为 POSIX errno 的影响太过深远，所以他被推选为 generic category；其他平台的 system category 内部实现需要负责自己平台的错误码映射并转换到 generic category 上。

这个 decision 也带来了几个影响：

- POSIX 上 generic category 和 system category 在具体实现上可以混用
- 可能存在有平台的错误码无法准确映射的情况，所以引入 `std::error_condition` 同时 `std::errc` 被定义成了 error condition，不要求 lose any information
- `std::error_code` 需要保留原始错误码，不然会出现 lose information

一个 3rd-party lib 也可以选择将自己的业务 error code 选择性地映射到 generic category 的 error condition 上，这也体现了 POSIX errno 的 generic。

### what about std::make_error_code

这个函数的存在是给类似 errc 的 enum class 做隐式转换用的，即用在 error code 的错误处理比较上。

你可以给自己的 enum class 提供重载版本，C++ 会利用 ADL 自动选择你的重载；所以这个函数的参数没有 error category，因为对应的 error category 是在函数实现里指定的

## Testing against error code directly

前面提到针对性处理 error code 时需要和 error condition 比较，例如 `std::errc`，但是这也存在一些例外。

如果你为自己的业务逻辑，或者 library，设计的 error code 体系能保证错误的原始值在不同的平台不同环境都是一致的，那么你的 enum class 直接代表 specific error code 也是可以的。

当然，其实这种情况反而是大多数情况…

举例来说，假设 `http::error` 代表 http 状态码中的错误码，那么

```cpp
namespace http {
enum class error {
  ...
  not_found = 404,
  ...
};
}

std::error_code ec;
http::get(url, ec);
if (ec) {
  // http::error::not_found will implicit convert to std::error_code
  if (ec == http::error::not_found) {
    // handle not found
  }
}
```

这样处理错误显然是合理的。

标准库提供的 [`std::io_errc`](https://en.cppreference.com/w/cpp/io/io_errc) 和 [`std::future_errc`](https://en.cppreference.com/w/cpp/thread/future_errc) 都是对应的 specific error code 而不是 error condition。

`std::errc` 的特殊性在前面讲过了，因为无法保证其他平台的系统调用错误码能完美映射到 POSIX error，所以才抽象为 error condition。

## Error enums

一个 error enum 在设计上即可以选择转换为对应的 error code 对象，也可以转换为 error condition 对象，具体的行为需要通过特化下面两个 type traits 完成：

```cpp
template <class T>
struct is_error_code_enum
  : public false_type {};

template <class T>
struct is_error_condition_enum
  : public false_type {};
```

对于一个 enum class E，只有当你特化了之后，C++ 才会试图通过 ADL 寻找匹配的 `make_error_code()` 或 `make_error_condition()`。

同时要注意这两个模板是定义在 std 名字空间中，他们的特化也要定义在 std 名字空间中。

## Practice: Let’s design our http client error-family

前面理论部分将的差不多了，现在通过给一个假想的 http client library 加上错误码体系来巩固理解。

完整代码见：[https://github.com/kingsamchen/Eureka/blob/master/cxx-define-specific-error-code-and-condition/errors/main.cpp](https://github.com/kingsamchen/Eureka/blob/master/cxx-define-specific-error-code-and-condition/errors/main.cpp)

### 0x0 Original error code

HTTP status code 比较特殊，2xx 代表成功，所以我们这里为了方便，跳过 1xx 和 2xx，从 3xx 开始考虑，并且只使用非正常状态码。

```cpp
namespace http {

//
// Original result/error code.
//

enum class error {
    // Redirection
    not_modified = 304,
    temporary_redirect = 307,
    permanent_rediret = 308,

    // Client error
    bad_request = 400,
    unauthorized = 401,
    forbidden = 403,
    not_found = 404,
    request_timeout = 408,
    conflict = 409,

    // Server error
    internal_server_error = 500,
    not_implemented = 501,
    bad_gateway = 502,
    service_unavailable = 503,
    gateway_timeout = 504,
};

class http_error_category_impl : public std::error_category {
public:
    ~http_error_category_impl() override = default;

    [[nodiscard]] const char* name() const noexcept override {
        return "http_error";
    }

    [[nodiscard]] std::string message(int ev) const override {
        switch (static_cast<error>(ev)) {
        case error::not_modified:
            return "Not Modified";
        case error::temporary_redirect:
            return "Temporary Redirect";
        case error::permanent_rediret:
            return "Permanent Redirect";
        case error::bad_request:
            return "Bad Request";
        case error::unauthorized:
            return "Unauthorized";
        case error::forbidden:
            return "Forbidden";
        case error::not_found:
            return "Not Found";
        case error::request_timeout:
            return "Request Timeout";
        case error::conflict:
            return "Conflict";
        case error::internal_server_error:
            return "Internal Server Error";
        case error::not_implemented:
            return "Not Implemented";
        case error::bad_gateway:
            return "Bad Gateway";
        case error::service_unavailable:
            return "Service Unavailable";
        case error::gateway_timeout:
            return "Gateway Timeout";
        default:
            return "Unknown http error";
        }
    }
};

const std::error_category& http_error_category() noexcept {
    static http_error_category_impl instance;
    return instance;
}

std::error_code make_error_code(error e) {
    return std::error_code(static_cast<int>(e), http_error_category());
}

} // namespace http

template<>
struct std::is_error_code_enum<http::error> : std::true_type {};
```

- `http::error` 代表最原始的 http status code
- 提供了对应的 http error category 用来解释每个具体的 code 的含义；其实现是个单例，这样是仿照标准库的做法
- 最后是通过特化 `std::is_error_code_enum` 允许从 enum 到 error code class 的自动转换

现在我们可以这样用

```cpp
void connect_always_fail(std::string_view addr, std::error_code& ec) {
    std::cout << "connecting to " << addr << "\n";
    // Because `is_error_code_enum<http::error>` is true, the corresponding overload is chosed,
    // and internally will use ADL to locate the `http::make_error_code` for error_code creation.
    ec = http::error::bad_request;
}

int main() {
    std::error_code ec;
    connect_always_fail("localhost", ec);
    if (ec && ec == http::error::bad_request) {
        std::cout << "connect failed: " << ec;
        auto econ = ec.default_error_condition();
        std::cout << "\necon: " << econ.value();
    }
    return 0;
}
```

因为我们没有重写 http error category 的 `default_error_condition()` 所以上述调用返回的 error_condition 包含相同的值和 http error category；这在不需要 fine-tune 的场景下是可以接受的。

### 0x1 Using general condition to separate client/server errors

在系列文章中，Christopher 通过重写 `default_error_condition()` 将 http error 关联到 `std::errc`；例如将 `http::error::unauthorized` 关联为 `std::errc::permission_denied`。

这里我们换一个做法，专门定义 error condition 将 4xx 错误从 5xx 区分开来；这个参考自 [Boost.System — Defining Library-Specific Error Conditions](https://www.boost.org/doc/libs/1_81_0/libs/system/doc/html/system.html#usage_defining_library_specific_error_conditions)：

```cpp
namespace http {

//
// General condition that will be the default condition of error.
//

enum class general_condition {
    need_redirect = 1, // any non-zero positive value is fine.
    client_side,
    server_side,
};

class http_condition_category_impl : public std::error_category {
public:
    ~http_condition_category_impl() override = default;

    [[nodiscard]] const char* name() const noexcept override {
        return "http_condition";
    }

    [[nodiscard]] std::string message(int cond) const override {
        switch (static_cast<general_condition>(cond)) {
        case general_condition::need_redirect:
            return "Need redirect request";
        case general_condition::client_side:
            return "Client side error";
        case general_condition::server_side:
            return "Server side error";
        default:
            return "Unknown http general condition";
        }
    }
};

const std::error_category& http_general_condition_category() noexcept {
    static http_condition_category_impl instance;
    return instance;
}

std::error_condition make_error_condition(general_condition e) noexcept {
    return std::error_condition(static_cast<int>(e), http_general_condition_category());
}

} // namespace http

template<>
struct std::is_error_condition_enum<http::general_condition> : std::true_type {};
```

- error condition enum 的值只要不为0就行，具体是什么我们并不关心
- 这个 general condition 同样需要自己的 general condition category 来进行解释
- 这次是通过特化 `std::is_error_condition_enum` 允许到 error condition 的自动转换

下面我们要重写 `http_error_category_impl::default_error_condition()` 将 http error 关联到 general condition：

```cpp
class http_error_category_impl : public std::error_category {
public:
    // ...

    [[nodiscard]]
    std::error_condition default_error_condition(int val) const noexcept override {
        switch (val / 100) {
        case 3:
            return make_error_condition(general_condition::need_redirect);
        case 4:
            return make_error_condition(general_condition::client_side);
        case 5:
            return make_error_condition(general_condition::server_side);
        default:
            // fallback to error_code self value.
            return std::error_condition(val, *this);
        }
    }
};
```

这样我们就可以在处理错误时判断某个 error code 是 client side error 还是 server side error 了：

```cpp
void connect_always_fail(std::string_view addr, int mock_resp_status, std::error_code& ec) {
    fmt::println("connecting to {}", addr);
    // Because `is_error_code_enum<http::error>` is true, the corresponding overload is chosed,
    // and internally will use ADL to locate the `http::make_error_code` for error_code creation.
    ec = static_cast<http::error>(mock_resp_status);
}

std::error_code ec;
connect_always_fail("localhost", static_cast<int>(http::error::bad_request), ec);
assert(ec);
assert(ec == http::error::bad_request);
assert(ec == http::general_condition::client_side);
```

### 0x3 Extending error handling condition

前面提到这套错误码系统是高度可扩展的，这里我们就针对某个 http error 是否可重试进行扩展。

```cpp
namespace http {

//
// Operation condition
//

enum class operation_condition {
    should_retry = 1,
};

class http_operation_condition_category_impl : public std::error_category {
public:
    ~http_operation_condition_category_impl() override = default;

    [[nodiscard]] const char* name() const noexcept override {
        return "http_operation_condition";
    }

    [[nodiscard]] std::string message(int val) const override {
        switch (static_cast<operation_condition>(val)) {
        case operation_condition::should_retry:
            return "Should retry later";
        default:
            return "Unknown http operation condition";
        }
    }

    [[nodiscard]] bool equivalent(const std::error_code& ec, int val) const noexcept override {
        switch (static_cast<operation_condition>(val)) {
        case operation_condition::should_retry:
            return ec == error::gateway_timeout ||
                   ec == error::conflict ||
                   ec == error::request_timeout ||
                   ec == error::service_unavailable;
        default:
            return false;
        }
    }
};

const std::error_category& http_operation_condition_category() noexcept {
    static http_operation_condition_category_impl instance;
    return instance;
}

std::error_condition make_error_condition(operation_condition e) noexcept {
    return std::error_condition(static_cast<int>(e), http_operation_condition_category());
}

} // namespace http

template<>
struct std::is_error_condition_enum<http::operation_condition> : std::true_type {};
```

非常类似的做法，只不过这次我们重写了 `http_operation_condition_category_impl::equivalent()` 针对 error code 的重载。

现在我们也可以做到

```cpp
std::error_code ec;
connect_always_fail("localhost", static_cast<int>(http::error::bad_request), ec);
assert(ec);
assert(ec == http::error::bad_request);
assert(ec == http::general_condition::client_side);
assert(ec != http::operation_condition::should_retry);
```

思考一下不难发现，虽然 error code 关联的 default error condition 只能有一个，但是参与错误处理的 error condition 是可以随意扩展的，只要重写 error condition category 的 equivalence 就行。

这样就能做到同一个具体错误在不同抽象层次不同语义下的处理

## References

[system-error-support-in-c0x-part-1.html](http://blog.think-async.com/2010/04/system-error-support-in-c0x-part-1.html)
[system-error-support-in-c0x-part-2.html](http://blog.think-async.com/2010/04/system-error-support-in-c0x-part-2.html)
[system-error-support-in-c0x-part-3.html](http://blog.think-async.com/2010/04/system-error-support-in-c0x-part-3.html)
[system-error-support-in-c0x-part-4.html](http://blog.think-async.com/2010/04/system-error-support-in-c0x-part-4.html)
[system-error-support-in-c0x-part-5.html](http://blog.think-async.com/2010/04/system-error-support-in-c0x-part-5.html)

[Boost.System Library](https://www.boost.org/doc/libs/1_81_0/libs/system/doc/html/system.html)