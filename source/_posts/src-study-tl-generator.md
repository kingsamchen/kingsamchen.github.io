---
title: TartanLlama/generator 源码分析
categories: PROGRAMMING
date: 2024-06-16 15:53:39
tags: [cpp, coroutine, generator]
---
Repo: https://github.com/TartanLlama/generator

## 核心三大件

核心还是 `generator` 和内部类 `promise`，因为需要支持 ranges，所以 generator 内部再引入一个 `iterator`

看管理 coroutine frame 的核心类只需要看定义的 `promise_type::get_return_object()` 的返回类型即可，绝大多数情况下这个类通常就是包含 `promise_type` 定义的类

## class generator

```cpp
struct promise;

using promise_type = promise;
using handle_type = std::coroutine_handle<promise_type>;
handle_type handle_ = nullptr;
```

通常情况下 generator 只有一个 coroutine handle 一个成员，coroutine handle 性质上很像 raw pointer

看看 special member functions 是怎么实现的

```cpp
class geneartor {
public:
   generator() noexcept = default;
   ~generator() {
      if (handle_) handle_.destroy();
   }

   generator(generator const&) = delete;
   generator(generator&& rhs) noexcept : handle_(std::exchange(rhs.handle_, nullptr)) {}
   generator& operator=(generator const&) = delete;
   generator& operator=(generator&& rhs) noexcept {
      swap(rhs);
      return *this;
   }

   void swap(generator& other) noexcept {
      std::swap(handle_, other.handle_);
   }

private:
   // Used by promise_type
   explicit generator(handle_type handle) noexcept : handle_(handle) {}
};
```

- 因为定义了给 promise 用的单参构造，所以还需要定义默认构造否则编译器不会生成
- 析构是很 conventional 的如果 coroutine handle 有效就 destroy

    <aside>
    💡 generator 使用场景需要手动管理资源，这个是由 promise 的 final_suspend 返回 always_suspend 实现的

    </aside>

- move ctor 实现的也很标准，利用 `std::exchange` 一招搞定
- move assignment 用了 swap 的 trick，这样可以延迟之前对象的销毁，而且 swap 的实现基本都是 noexcept 的

## class promise_type

```cpp
struct promise {
   using value_type = std::remove_reference_t<T>;
   using reference_type = std::conditional_t<std::is_pointer_v<value_type>, value_type, value_type&>;
   using pointer_type = std::conditional_t<std::is_pointer_v<value_type>, value_type, value_type*>;

   promise() = default;

   generator get_return_object() {
      return generator(std::coroutine_handle<promise>::from_promise(*this));
   }

   std::suspend_always initial_suspend() const { return {}; }
   std::suspend_always final_suspend() const noexcept { return {}; }

   void return_void() const noexcept { return; }

   void unhandled_exception() noexcept {
      exception_ = std::current_exception();
   }

   void rethrow_if_exception() {
      if (exception_) {
         std::rethrow_exception(exception_);
      }
   }

   std::suspend_always yield_value(reference_type v) noexcept {
      if constexpr (std::is_pointer_v<value_type>) {
         value_ = v;
      } else {
         value_ = std::addressof(v);
      }
      return {};
   }

   std::exception_ptr exception_;
   pointer_type value_;
};
```

因为 promise 通常是由编译器插桩代码调用的，所以甚至不用考虑 special member functions，直接实现为 value semantics 的 struct 结构即可。

promise 结构需要实现六个特殊函数：

- `get_return_object()`: 这个函数负责返回管理 coroutine handle 的对象，实现体非常固定，绝大多数情况下没有第二种写法
- `initial_suspend()` 和 `final_suspend()`: 函数如其名。final_suspend 如果会 suspend，则需要自己手动清理 coroutine frame；否则编译器会自动清理

    generator 场景下一般都是自己手动清理

- `unhandled_exception()`: 有异常时调用；一般直接把 exception pointer 存下来就行

    rethrow 这个如果没有函数的话就需要在 resume coroutine 的时候手动写全，看自己喜好

- `return_void()` / `return_value()`: co_return 调用前者，co_return value 调用后者，这俩不能都不实现；co_return 会触发 coroutine 结束
- `yield_value()`: 等价于

    ```cpp
    co_await promise.yield_value(expr)
    ```


---

另外这个 promise 实现有个地方比较有意思，他存储的是指针

- 如果 co_yield 的值不是指针，他存的是值得指针
- 如果 co_yield 的就是指针，那就拷贝一下

Ref 得资源分配到了 coroutine handle 管理的 heap 上，所以这里存一个指针也是可以的，并且可以减少不必要的拷贝

但是这里有个坑，没法直接对 integer literal 做 co_yield 了，因为 prvalue 没有地址…

## iterator

这里的 iterator 只做了基本支持，并且目标是为了能够兼容 range concept，一个明显的点是没有定义 iterator category

```cpp
      public:
         using value_type = typename promise_type::value_type;
         using reference_type = typename promise_type::reference_type;
         using pointer_type = typename promise_type::pointer_type;
         using difference_type = std::ptrdiff_t;
```

前三个 type 都是定义自 `promise_type`。

```cpp
class iterator {
using handle_type = std::coroutine_handle<promise_type>;

public:
   iterator() = default;
   ~iterator() {
      if (handle_) handle_.destroy();
   }

   //Non-copyable because coroutine handles point to a unique resource
   iterator(iterator const&) = delete;
   iterator(iterator&& rhs) noexcept : handle_(std::exchange(rhs.handle_, nullptr)) {}
   iterator& operator=(iterator const&) = delete;
   iterator& operator=(iterator&& rhs) noexcept {
      handle_ = std::exchange(rhs.handle_, nullptr);
      return *this;
   }

private:
   friend class generator;
   iterator(handle_type handle) : handle_(handle) {}  // used by generator::begin

   handle_type handle_;
};

// =>

// generator::begin
iterator begin() {
   handle_.resume();
   if (handle_.done()) {
      handle_.promise().rethrow_if_exception();
   }
   return {std::exchange(handle_, nullptr)};
}
```

- 因为 coroutine handle 比较特殊，所以选择让 iterator 不支持 copy；move 还是支持的，并且实现和 generator 的差不多
- `generator::begin()` 调用之后会将 coroutine handle 转移到 iterator（通过 `iterator(handle_type handle)` 函数）；这个构造函数被实现为 private 可能是不想直接暴露，所以 generator 只能作为 friend 使用
- 一旦 generator 转移了 coroutine handle，这个 generator 就空了，也没法在做下一次的 begin，这个其实比较反传统；所以 iterator 的析构也要负责回收 coroutine frame 关联的资源

<aside>
💡 `begin()` 里先要调一次 `resume()` 放到后面再解释

</aside>

然后再来看迭代器中非常重要的自增和解引用

```cpp
iterator& operator++() {
   handle_.resume();
   if (handle_.done()) {
      handle_.promise().rethrow_if_exception();
   }
   return *this;
}

void operator++(int) {
   (void)this->operator++();
}

reference_type operator*() const
   noexcept(noexcept(std::is_nothrow_copy_constructible_v<reference_type>)){
   if constexpr (std::is_pointer_v<value_type>)
      return handle_.promise().value_;
   else
      return *handle_.promise().value_;
}
```

因为每次自增都等价于需要切换到 `co_yield` 的协程，所以需要 resume coroutine

前置自增一样的效果，因为不需要返回 previous value

解引用是直接从 promise 中拿保存下来的值：

- 这个值是编译器调用 promise 的 yield_value 塞进去的；前面提到了 promise 实际存的是指针，所以这里也需要做一个类似的处理
- coroutine handle 能拿到关联的 promise 对象

那么 generator 怎么判断结束呢？答案是利用 `sentinel` 对象，这个是 C++ 17 开始支持的 relax

```cpp
class sentinel {};

sentinel generator::end() const noexcept {
   return {};
}

friend bool operator==(iterator const& it, sentinel) noexcept {
   return (!it.handle_ || it.handle_.done());
}
```

这样就可以把迭代器的结束转换为自身状态的判断，而不用再费心思去构造 end iterator。

## coroutine control flow 从 generator::begin 说起

一开始没弄明白为什么 `generator::begin()` 的代码会要 resume coroutine，于是加了一点代码

```diff
--- a/include/tl/generator.hpp
+++ b/include/tl/generator.hpp
@@ -38,10 +38,14 @@ namespace tl {
          promise() = default;

          generator get_return_object() {
+            fprintf(stderr, "get_return_object\n");
             return generator(std::coroutine_handle<promise>::from_promise(*this));
          }

-         std::suspend_always initial_suspend() const { return {}; }
+         std::suspend_always initial_suspend() const {
+            fprintf(stderr, "initial suspend\n");
+            return {};
+         }
          std::suspend_always final_suspend() const noexcept { return {}; }

          void return_void() const noexcept { return; }
@@ -57,6 +61,7 @@ namespace tl {
          }

          std::suspend_always yield_value(reference_type v) noexcept {
+            fprintf(stderr, "yield value\n");
             if constexpr (std::is_pointer_v<value_type>) {
                value_ = v;
             } else {
@@ -160,7 +165,9 @@ namespace tl {

    private:
       friend class iterator;
-      explicit generator(handle_type handle) noexcept : handle_(handle) {}
+      explicit generator(handle_type handle) noexcept : handle_(handle) {
+         fprintf(stderr, "generator::generator(handle_type handle)\n");
+      }

       handle_type handle_ = nullptr;
    };
diff --git a/tests/test.cpp b/tests/test.cpp
index f963f42..9a8fbb3 100644
--- a/tests/test.cpp
+++ b/tests/test.cpp
@@ -91,9 +91,13 @@ seven eight nine)";
 }

 tl::generator<const char*> generate() {
+   fprintf(stderr, "stage 1\n");
    co_yield "one";
+   fprintf(stderr, "stage 2\n");
    co_yield "two";
+   fprintf(stderr, "stage 3\n");
    co_yield "three";
+   fprintf(stderr, "stage 4\n");
 }

 TEST_CASE("pointers") {
@@ -106,3 +110,16 @@ TEST_CASE("pointers") {
       ++i;
    }
 }
+
+TEST_CASE("Debug") {
+   fprintf(stderr, "before generate()\n");
+   auto gen = generate();
+   fprintf(stderr, "before begin()\n");
+   auto it = gen.begin();
+   fprintf(stderr, "after begin()\n");
+   REQUIRE(*it == std::string_view{"one"});
+   fprintf(stderr, "before ++()\n");
+   ++it;
+   fprintf(stderr, "after ++()\n");
+   REQUIRE(*it == std::string_view{"two"});
+}

```

输出：

```bash
$ build/tl-generator-test-test "Debug"
Filters: Debug
before generate()
get_return_object
generator::generator(handle_type handle)
initial suspend # <--
before begin()
stage 1
yield value
after begin()
before ++()
stage 2
yield value
after ++()
===============================================================================
All tests passed (2 assertions in 1 test case)
```

- `generate()` 函数包含 `co_yield` 于是被编译器处理成 coroutine
- coroutine frame 的 initial suspend 是发生在 `generate()` 函数第一条语句前
- 因为 promise 的 initial suspend 返回的是 always suspend，所以控制流转到主流程的时候 coroutine 已经是 suspended
- 等到 coroutine resume 之后，才会开始执行函数体

这个 flow 可以看 cppreference [https://en.cppreference.com/w/cpp/language/coroutines](https://en.cppreference.com/w/cpp/language/coroutines)
