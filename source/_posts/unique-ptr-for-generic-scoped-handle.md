---
title: unique_ptr For Generic Scoped Handle
categories: PROGRAMMING
date: 2021-11-09 22:43:31
tags: [cpp]
---
之前在 {% post_link wrap-win32-handle-into-unique-ptr %} 中介绍了如何基于 `std::unique_ptr` 快速实现对 Windows HANDLE 的 RAII 化。

但是如果我们也要对 `fd` 或者 Windows 上的 `SOCKET` 做类似的处理，重复实现 `xx_handle` 和 `xx_handle_deleter` 就显得过于琐碎。

实际上我们可以利用模板参数注入，将共同点抽象出来，不同点实现成各自的 type traits，然后利用模板进行注入。

这样一来，我们只需要简单实现每个 handle type 对应的基础属性，就可以直接复用核心实现。

```c++
template<typename Traits>
class handle_ptr {
public:
    using handle_type = typename Traits::handle_type;

    handle_ptr() noexcept = default;

    // implicit
    handle_ptr(std::nullptr_t) noexcept {}

    // implicit
    handle_ptr(handle_type handle) noexcept
        : handle_(handle) {}

    ~handle_ptr() = default;

    explicit operator bool() const noexcept {
        return Traits::is_valid(handle_);
    }

    // implicit
    operator handle_type() const noexcept {
        return handle_;
    }

    friend bool operator==(handle_ptr lhs, handle_ptr rhs) noexcept {
        return lhs.handle_ == rhs.handle_;
    }

    friend bool operator!=(handle_ptr lhs, handle_ptr rhs) noexcept {
        return !(lhs == rhs);
    }

private:
    handle_type handle_{Traits::null_handle};
};

template<typename Traits>
struct handle_ptr_deleter {
    using pointer = handle_ptr<Traits>;

    void operator()(pointer ptr) {
        Traits::close(ptr);
    }
};
```

PS：不同于文章中的例子，基底类起名 `handle_ptr` 更为确切；因为在 `unique_ptr` 的内部会用来定义 `pointer`；而几乎 unique_ptr 的语义行为都是围绕 `pointer` 来完成的。

有了上面这个基底之后，针对 fd-wrapper，我们只需要

```c++
struct fd_traits {
    using handle_type = int;

    static bool is_valid(handle_type handle) noexcept {
        return handle != null_handle;
    }

    static void close(handle_type handle) noexcept {
        ::close(handle);
    }

    static constexpr handle_type null_handle{-1};
};

using fd_deleter = handle_ptr_deleter<fd_traits>;
using unique_fd = std::unique_ptr<fd_traits::handle_type, fd_deleter>;

inline unique_fd wrap_unique_fd(int raw_fd) {
    return unique_fd(raw_fd);
}
```

这样一来，实现一个新的 unique handle wrapper，我们只需要确定四个东西：

1. handle type 的实际类型
2. null handle 的值，用来确定 default/zero initialized 之后 handle 状态
3. 实现 `is_valid()`
4. 实现 `close()`

剩下的就是常规的类型实例化。

<!-- more -->
