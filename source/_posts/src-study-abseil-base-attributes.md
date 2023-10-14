---
title: abseil base/attributes 源码分析
categories: PROGRAMMING
date: 2023-10-15 00:15:26
tags: [cpp, abseil]
---
## 标准库检测

C++ 20 开始终于有了 feature testing macro `__has_cpp_attribute()`, 见 https://en.cppreference.com/w/cpp/feature_test

```cpp
#ifdef __has_cpp_attribute                      // Check if __has_cpp_attribute is present
#  if __has_cpp_attribute(deprecated)           // Check for an attribute
#    define DEPRECATED(msg) [[deprecated(msg)]]
#  endif
#endif
#ifndef DEPRECATED
#    define DEPRECATED(msg)
#endif
```

## Explicitly mark as weak symbol

`__attribute__((weak))`

Used to declare a weak symbol, which can be overriden by a strong symbol at link time.

```cpp
void my_function() __attribute__((weak));
```

## Notnull tags

`__attribute__((nonnull(arg_index)))`  

`__attribute__((returns_nonnull))`

They only can check at compile-time and will be UB of violoated at runtime.

and can use syntax `__attribute__((nonnull(arg_index1, arg_index2)))` for multiple args.

## Sanitizers related

`__attribute__((no_sanitize_address))` or in MSVC `__declspec(no_sanitize_address)` to ignore a given function on memory address issues

`__attribute__((no_sanitize_memory))` supported by Clang only; and handles with uninitialized memory issues

`__attribute__((no_sanitize_thread))` for tsan, and

`__attribute__((no_sanitize("undefined")))` for ubsan

`__attribute__((no_sanitize("cfi")))` for CFI(Control Flow Integrity)

https://clang.llvm.org/docs/ControlFlowIntegrity.html

`__attribute__((no_sanitize("safe-stack")))` for safe stack

For safe-stack sanitizer https://clang.llvm.org/docs/SafeStack.html

## constinit

```cpp
#if defined(__cpp_constinit) && __cpp_constinit >= 201907L
#define ABSL_CONST_INIT constinit
#elif ABSL_HAVE_CPP_ATTRIBUTE(clang::require_constant_initialization)
#define ABSL_CONST_INIT [[clang::require_constant_initialization]]
#else
#define ABSL_CONST_INIT
#endif
```

## Lifetime bound

```cpp
#if ABSL_HAVE_CPP_ATTRIBUTE(clang::lifetimebound)
#define ABSL_ATTRIBUTE_LIFETIME_BOUND [[clang::lifetimebound]]
#elif ABSL_HAVE_ATTRIBUTE(lifetimebound)
#define ABSL_ATTRIBUTE_LIFETIME_BOUND __attribute__((lifetimebound))
#else
#define ABSL_ATTRIBUTE_LIFETIME_BOUND
#endif
```

https://clang.llvm.org/docs/AttributeReference.html#lifetimebound

