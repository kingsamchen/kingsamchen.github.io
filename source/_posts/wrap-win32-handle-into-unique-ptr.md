---
title: 用 unique_ptr 管理 Windows HANDLE
categories: PROGRAMMING
date: 2021-06-14 17:16:27
tags: [cpp, unique_ptr]
---
作为一个曾经的资深 Windows developer，Windows `HANDLE` 一个不引人注意的但是绝对值得引起注意的地方是，它的无效值表示是不唯一的。

除了最常见的 `NULL` 之外还有一个 `INVALID_HANDLE_VALUE`，并且即使是在数值上这两个东西也不一样。

如果要为 `HANDLE` 写一个 C++ RAII Wrapper 那么必须要考虑这个点，比如 Chromium 就是[这么做的](https://chromium.googlesource.com/chromium/src/+/refs/heads/main/base/win/scoped_handle.h#116)。

Chromium 的这个设施早在 C++ 11 之前就有了，并且经过多年来的迭代已经变得异常复杂；如果你自己需要这样一个东西，又无法从 Chromium 这种工业项目中抽取一个组件的话，只能考虑自己实现。

但是要写对一个工业强度的 handle-wrapper 并不容易，同时还要考虑到通用性和实用性，比如可能后续要支持 fd/socket，Windows HDC 等各种奇奇怪怪的东西。

所以能否用 `std::unique_ptr` 来作为基础实现，针对一些特定细节做泛化？

这就引出一个问题：如果希望用 `std::unique_ptr` 来作为 `HANDLE` 的一个 RAII 包装，如何正确判断一个 handle 是否 valid？

更进一步，问题还可以变为：如何用 `std::unique_ptr` 管理 pointer-like 的资源？
<!-- more -->
答案是 `std::unique_ptr` 在设计上还真考虑到了这种场景：

> Unlike `std::shared_ptr`, `std::unique_ptr` may manage an object through any custom handle type that satisfies `NullablePointer`.

`std::unique_ptr` 允许通过内部的 custom handle 来管理对象资源，只要这个 custom handle 满足 `NullablePointer`。

根据 https://en.cppreference.com/w/cpp/memory/unique_ptr 里的 _Member Types_，可以发现，只要 `Deleter::pointer` 是有定义的，`std::unique_ptr::pointer` 就会定义为 `Deleter::poiner`，否则才是 `T*`。

而 [NullablePoiner](https://en.cppreference.com/w/cpp/named_req/NullablePointer) 则定义了 custom handle object 和 `std::nullptr_t` 的关系，并且文档里给了一个 minimalistic 的实现。

---

所以我们只需要

1. 给 `HANDLE` 先做一个 `win_handle` 的 custom handle class，并且这个 class 满足 nullable-pointer requirements；同时正确实现 `explicit operator bool()`

2. 定义一个 `win_handle_deleter`，内部定义了 pointer 为 `win_handle`

3. 和 `std::unique_ptr` 组合在一起

大概长这样：

```cpp
class win_handle {
public:
    win_handle() noexcept = default;

    // implicit
    win_handle(std::nullptr_t) noexcept {}

    // implicit
    win_handle(HANDLE h)
        : handle_(h) {}

    explicit operator bool() const noexcept {
        return handle_ != nullptr && handle_ != INVALID_HANDLE_VALUE;
    }

    // implicit
    operator HANDLE() const noexcept {
        return handle_;
    }

    friend bool operator==(win_handle lhs, win_handle rhs) noexcept {
        return lhs.handle_ == rhs.handle_;
    }

    friend bool operator!=(win_handle lhs, win_handle rhs) noexcept {
        return !(lhs == rhs);
    }

private:
    HANDLE handle_ = nullptr;
};

struct win_handle_deleter {
    using pointer = win_handle;

    void operator()(pointer ptr) const {
        details::ignore_result(CloseHandle(ptr));
    }
};

static_assert(std::is_empty_v<win_handle_deleter>);

using scoped_win_handle = std::unique_ptr<win_handle, win_handle_deleter>;
```

随便找个测试框架或者新建工程测试一下：

```cpp
TEST_CASE("For Windows Handle", "[windows-handle]") {
    scoped_win_handle handle;
    REQUIRE(!handle);

    SECTION("equivalent to one that being initialized with INVALID_HANDLE_VALUE") {
        scoped_win_handle invalid_handle(INVALID_HANDLE_VALUE);
        CHECK(handle != invalid_handle);
        CHECK((!handle && !invalid_handle));
    }

    SECTION("unique_ptr abilities") {
        handle.reset(CreateEventW(nullptr, TRUE, FALSE, L"test_ev"));
        REQUIRE(!!handle);

        auto dup = std::move(handle);
        CHECK(!handle);
        CHECK(!!dup);
    } // the Event kernel object is destroyed at here.

    handle.reset(OpenEventW(EVENT_ALL_ACCESS, FALSE, L"test_ev"));
    CHECK(!handle);
    CHECK(GetLastError() == ERROR_FILE_NOT_FOUND);
}
```

其他资源也可以用类似的手法进行二次包装处理。

---

1. 关于为什么 `HANDLE` 会有 `NULL` 和 `INVALID_HANDLE_VALUE`，参阅考据帝 Raymond Chen 的[这篇文章](https://devblogs.microsoft.com/oldnewthing/20040302-00/?p=40443)
2. 上面文章链接里面也提到了一个更“可怕”的事情：`GetCurrentProcess()` 返回的 pseudo-handle 在数值上恰好等于 `INVALID_HANDLE_VALUE`，但是在语义上，pseudo-handle 是 valid 的。
