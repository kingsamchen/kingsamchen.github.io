---
title: 浅析 shared_ptr：Libstdc++ 篇
categories: PROGRAMMING
date: 2018-03-30 00:58:44
tags: [shared_ptr-internals, source-code-study, shared_ptr, weak_ptr, c++, libstdc++, gcc, clang]
---
序言请移步[此处](https://kingsamchen.github.io/2018/03/13/shared-ptr-internals-introduction/)

MSVC STL 的分析版本请移步[此处](https://kingsamchen.github.io/2018/03/16/demystify-shared-ptr-and-weak-ptr-in-msvc-stl/)

注：因为这不是第一篇分析，所以会直入主题，跳过文学写作常用的累赘的过渡。

## 版本选择与源码位置

目前的工作机是 Linux Mint 18，对应的是 Ubuntu 16.04 LTS。

这个版本的系统上源包默认提供的是 GCC 5.4 和 clang 3.8，跟随的 libstdc++ 的大版本是 6。

考虑到 Linux 上 clang 也是默认使用 libstdc++，且 GCC 6/7/8 使用的 libstdc++ 的大版本都是 6，因此直接选用目录 `/usr/include/c++/5` 下的源码作为研究对象。
<!-- more -->

## How shared_ptr(new T()) differs from make_shared()

函数 `make_shared()` 位于文件 shared_ptr.h：

```cpp
/**
*  @brief  Create an object that is owned by a shared_ptr.
*  @param  __args  Arguments for the @a _Tp object's constructor.
*  @return A shared_ptr that owns the newly created object.
*  @throw  std::bad_alloc, or an exception thrown from the
*          constructor of @a _Tp.
*/
template<typename _Tp, typename... _Args>
inline shared_ptr<_Tp>
make_shared(_Args&&... __args)
{
    typedef typename std::remove_const<_Tp>::type _Tp_nc;
    return std::allocate_shared<_Tp>(std::allocator<_Tp_nc>(), std::forward<_Args>(__args)...);
}
```

函数内部直接将操作转交给了 `std::allocate_shared`。这个函数虽然是 [C++ 11 引入](http://en.cppreference.com/w/cpp/memory/shared_ptr/allocate_shared)的，但是似乎是从 boost 沿袭而来，我们在 MSVC STL 的分析里并未见到过。

注：reference 上对这个函数的描述简洁明了，点名了就是用来实现一次内存分配的。事实上截止到 Visual Studi 2017 15.6.3，这个函数还只在 TR1 名字空间里。

```cpp
/**
*  @brief  Create an object that is owned by a shared_ptr.
*  @param  __a     An allocator.
*  @param  __args  Arguments for the @a _Tp object's constructor.
*  @return A shared_ptr that owns the newly created object.
*  @throw  An exception thrown from @a _Alloc::allocate or from the
*          constructor of @a _Tp.
*
*  A copy of @a __a will be used to allocate memory for the shared_ptr
*  and the new object.
*/
template<typename _Tp, typename _Alloc, typename... _Args>
inline shared_ptr<_Tp>
allocate_shared(const _Alloc& __a, _Args&&... __args)
{
    return shared_ptr<_Tp>(_Sp_make_shared_tag(), __a,
                std::forward<_Args>(__args)...);
}

// This constructor is non-standard, it is used by allocate_shared.
template<typename _Alloc, typename... _Args>
shared_ptr(_Sp_make_shared_tag __tag, const _Alloc& __a, _Args&&... __args)
    : __shared_ptr<_Tp>(__tag, __a, std::forward<_Args>(__args)...)
{}
```

这里的 `__shared_ptr` 是 `shared_ptr` 的基类，相当于 MSVC STL 里的 `_Ptr_base`，包含在文件 `shared_ptr_base.h` 里。

我们分别看一下 `__shared_ptr` 的基本成员，以及这个非标准的构造函数

```cpp
template<typename _Tp, _Lock_policy _Lp>
class __shared_ptr {
    // Omit irrelavent code
private:
    _Tp*	   	   _M_ptr;         // Contained pointer.
    __shared_count<_Lp>  _M_refcount;    // Reference counter.
};

// This constructor is non-standard, it is used by allocate_shared.
template<typename _Alloc, typename... _Args>
__shared_ptr(_Sp_make_shared_tag __tag, const _Alloc& __a, _Args&&... __args)
    : _M_ptr(),
      _M_refcount(__tag, (_Tp*)0, __a, std::forward<_Args>(__args)...)
{
    // _M_ptr needs to point to the newly constructed object.
    // This relies on _Sp_counted_ptr_inplace::_M_get_deleter.
    void* __p = _M_refcount._M_get_deleter(typeid(__tag));
    _M_ptr = static_cast<_Tp*>(__p);
    __enable_shared_from_this_helper(_M_refcount, _M_ptr, _M_ptr);
}
```

注意，这个构造函数有两个版本的实现，分别对应是否开启 RTTI，这里选用默认的使用 RTTI 的版本。

基类也是有两个成员：
1. 实例指针
2. 引用计数控制块

实际的构造操作依旧由引用计数控制块 `__shared_count` 完成。

```cpp
template<_Lock_policy _Lp>
class __shared_count {
    // Omit irrelavent code
private:
    _Sp_counted_base<_Lp>*  _M_pi;
};

template<typename _Tp, typename _Alloc, typename... _Args>
__shared_count(_Sp_make_shared_tag, _Tp*, const _Alloc& __a, _Args&&... __args)
: _M_pi(0)
{
    typedef _Sp_counted_ptr_inplace<_Tp, _Alloc, _Lp> _Sp_cp_type;
    typename _Sp_cp_type::__allocator_type __a2(__a);
    auto __guard = std::__allocate_guarded(__a2);
    _Sp_cp_type* __mem = __guard.get();
    ::new (__mem) _Sp_cp_type(std::move(__a),
                std::forward<_Args>(__args)...);
    _M_pi = __mem;
    __guard = nullptr;
}
```

这部分实现比 MSVC STL 要复杂，因为牵扯到了 allocator。

实话说，我对 allocator 不熟悉，也从来没用过 customized allocator；同时这里使用的 allocator 相关函数大多是 libstdc++ 自己定义的，因此这块仅在宏观上提一下，不做深入分析。有兴趣的可以自己跟踪一下源代码。


首先，`_Sp_counted_ptr_inplace` 是 `_Sp_counted_base` 的子类，这个类有一个内部类，并且类自身只有一个这个内部类的成员（略去了不相关代码）：

```cpp
template<_Lock_policy _Lp = __default_lock_policy>
class _Sp_counted_base : public _Mutex_base<_Lp> {
    // ...

    _Atomic_word  _M_use_count;     // #shared
    _Atomic_word  _M_weak_count;    // #weak + (#shared != 0)
};

template<typename _Tp, typename _Alloc, _Lock_policy _Lp>
class _Sp_counted_ptr_inplace final : public _Sp_counted_base<_Lp> {
    class _Impl : _Sp_ebo_helper<0, _Alloc> {
        typedef _Sp_ebo_helper<0, _Alloc>	_A_base;

        __gnu_cxx::__aligned_buffer<_Tp> _M_storage;
    };

    _Impl _M_impl;
};
```

可以看出，父类 `_Sp_counted_base` 包含了两个计数成员，同时子类 `_Sp_counted_ptr_inplace` 通过内部的 `_Impl`，引入了一块存储空间，大小是最外层 `share_ptr` 要管理的对象实例的大小。

这样一来，被管理对象的内存和控制块的内存也被组织在了一块内存上。

接下来研究如何分配那整块的内存；这和前面三句：

```cpp
typename _Sp_cp_type::__allocator_type __a2(__a);
auto __guard = std::__allocate_guarded(__a2);
_Sp_cp_type* __mem = __guard.get();
```

有关。

上面通过 `_Sp_counted_ptr_inplace` 内部定义的 allocator，利用 [allocator rebind](http://en.cppreference.com/w/cpp/memory/allocator)，获取能够以 `_Sp_counted_ptr_inplace` 为粒度进行分配的 allocator，并随后分配了一块内存，也就是 `_mem` 引用的那块。

有了内存块之后，剩下的就是调用 in-placement new 构造出 `_Sp_counted_ptr_inplace` 对象。

接下来看一下顶层的 `__Shared_ptr::_M_ptr` 是如何关联到 `_Sp_counted_ptr_inplace` 里的实例成员地址的。

```cpp
// _M_ptr needs to point to the newly constructed object.
// This relies on _Sp_counted_ptr_inplace::_M_get_deleter.
void* __p = _M_refcount._M_get_deleter(typeid(__tag));
_M_ptr = static_cast<_Tp*>(__p);
```

依赖 `_M_get_deleter` 是什么鬼...看一下代码

```cpp
// class __shared_count
void* _M_get_deleter(const std::type_info& __ti) const const const noexcept
{
    return _M_pi ? _M_pi->_M_get_deleter(__ti) : nullptr;
}

// class _Sp_counted_ptr_inplace
// Sneaky trick so __shared_ptr can get the managed pointer
virtual void* _M_get_deleter(const std::type_info& __ti) noexcept
{
#if __cpp_rtti
	if (__ti == typeid(_Sp_make_shared_tag))
	  return const_cast<typename remove_cv<_Tp>::type*>(_M_ptr());
#endif
	return nullptr;
}

_Tp* _M_ptr() noexcept
{
    return _M_impl._M_storage._M_ptr();
}
```

其实就是拿到 `_Sp_counted_ptr_inplace::Impl` 内部 `__gnu_cxx::__aligned_buffer` 的数据地址。

这里的 RTTI 的操作看得我一脸懵逼……

接下来看一下针对形如

```cpp
std::shared_ptr<T> sp(new T());
```

的内部实现流程。

```cpp
/**
 *  @brief  Construct a %shared_ptr that owns the pointer @a __p.
 *  @param  __p  A pointer that is convertible to element_type*.
 *  @post   use_count() == 1 && get() == __p
 *  @throw  std::bad_alloc, in which case @c delete @a __p is called.
 */
template<typename _Tp1>
explicit shared_ptr(_Tp1* __p)
    : __shared_ptr<_Tp>(__p)
{}

template<typename _Tp1>
explicit __shared_ptr(_Tp1* __p)
    : _M_ptr(__p), _M_refcount(__p) // <- set up instance ptr & ref-count object
{
    __glibcxx_function_requires(_ConvertibleConcept<_Tp1*, _Tp*>)
    static_assert( !is_void<_Tp1>::value, "incomplete type" );
    static_assert( sizeof(_Tp1) > 0, "incomplete type" );
    __enable_shared_from_this_helper(_M_refcount, __p, __p);
}

template<typename _Ptr>
explicit __shared_count(_Ptr __p)
    : _M_pi(0)
{
    __try
    {
        _M_pi = new _Sp_counted_ptr<_Ptr, _Lp>(__p);    // <- use _Sp_counted_ptr as ref-count object here
    }
    __catch(...)
    {
        delete __p;
        __throw_exception_again;
    }
}
```

可以看出，在这种使用场合下，ref-count 的具体数据类型是 `_Sp_counted_ptr`：

```cpp
// Counted ptr with no deleter or allocator support
template <typename _Ptr, _Lock_policy _Lp>
class _Sp_counted_ptr final : public _Sp_counted_base<_Lp> {
public:
    explicit _Sp_counted_ptr(_Ptr __p) noexcept
        : _M_ptr(__p)
    {}

    virtual void _M_dispose() noexcept
    {
        delete _M_ptr;
    }

    virtual void _M_destroy() noexcept
    {
        delete this;
    }

    virtual void* _M_get_deleter(const std::type_info&) noexcept
    {
        return nullptr;
    }

    _Sp_counted_ptr(const _Sp_counted_ptr&) = delete;
    _Sp_counted_ptr& operator=(const _Sp_counted_ptr&) = delete;

private:
    _Ptr _M_ptr;
};
```

仅仅调用构造函数初始化了内部保存的 `_M_ptr`，毕竟管理的实例对象已经创建完毕了。

至此整个初始化流程都已经结束了。


**Conclusion**

和 MSVC STL 的实现类似，`make_shared()` 内部保证只有一次内存分配，实例对象和计数控制块都在一块内存上，而 `shared_ptr()` 的构造就简单很多，只需要初始化计数块，同时设置对应的实例指针即可。

并且，两种 case 下，具体使用的 ref-count 的对象是不一样的，这可以做到针对性的优化。

不同点也比较明显：libstdc++ STL 多了一个 `shared_count` 中间层；同时 `make_shared()` 内部的内存分配大量依赖 allocator。

## Why Virtual Dtor is Not Necessary When Deleting From a Base Pointer

这种 case 是通过 `shared_ptr` 的构造函数创建对象，只需要跟相关的 ctor 即可。

和 MSVC STL 类似，ctor 自身也是一个 template，参数为 `_Tp1`，并且这个参数一直通过 `__shared_ptr` - `__shared_count` - `_Sp_counted_ptr` 的 ctor template 将实际类型保存到了 `_Sp_counted_ptr::_M_ptr` 中。

释放实例的 `_Sp_counted_ptr::_M_dispose()` 仅仅简单的调用了一下 delete expression

```cpp
virtual void _M_dispose() noexcept
{
    delete _M_ptr;
}
```

## How Custom Deleter Works and Why It Is Not Part of the Type

支持 custom deleter 同样需要通过构造函数创建对应的 `shared_ptr` 对象。

```cpp
/**
 *  @brief  Construct a %shared_ptr that owns the pointer @a __p
 *          and the deleter @a __d.
 *  @param  __p  A pointer.
 *  @param  __d  A deleter.
 *  @post   use_count() == 1 && get() == __p
 *  @throw  std::bad_alloc, in which case @a __d(__p) is called.
 *
 *  Requirements: _Deleter's copy constructor and destructor must
 *  not throw
 *
 *  __shared_ptr will release __p by calling __d(__p)
 */
template <typename _Tp1, typename _Deleter>
shared_ptr(_Tp1* __p, _Deleter __d)
    : __shared_ptr<_Tp>(__p, __d)
{}
```

类似的，直接托管给负责干活的父类 `__shared_ptr`：

```cpp
template <typename _Tp1, typename _Deleter>
__shared_ptr(_Tp1* __p, _Deleter __d)
    : _M_ptr(__p),
      _M_refcount(__p, __d)
{
    __glibcxx_function_requires(_ConvertibleConcept<_Tp1*, _Tp*>)
    // TODO requires _Deleter CopyConstructible and __d(__p) well-formed
    __enable_shared_from_this_helper(_M_refcount, __p, __p);
}
```

接着又通过构造函数参数转入计数控制块 `_M_refcount`：

```cpp
template <typename _Ptr, typename _Deleter>
__shared_count(_Ptr __p, _Deleter __d)
    : __shared_count(__p, std::move(__d), allocator<void>())
{}

template <typename _Ptr, typename _Deleter, typename _Alloc>
__shared_count(_Ptr __p, _Deleter __d, _Alloc __a)
    : _M_pi(0)
{
    typedef _Sp_counted_deleter<_Ptr, _Deleter, _Alloc, _Lp> _Sp_cd_type;
    __try
    {
        typename _Sp_cd_type::__allocator_type __a2(__a);
        auto __guard = std::__allocate_guarded(__a2);
        _Sp_cd_type* __mem = __guard.get();
        ::new (__mem) _Sp_cd_type(__p, std::move(__d), std::move(__a));
        _M_pi = __mem;
        __guard = nullptr;
    }
    __catch(...)
    {
        __d(__p); // Call _Deleter on __p.
        __throw_exception_again;
    }
}
```

从上面可以看出：

1. 这种情况下使用的具体 ref-count 类型是 `_Sp_counted_deleter`
2. 这里的内存分配依然是通过 allocator
3. deleter 和托管实例的类型通过 template paramter 一路保存了下来。因为 deleter 可能只认识创建时传递的指针类型（考虑 aliasing construction）

```cpp
// Support for custom deleter and/or allocator
template <typename _Ptr, typename _Deleter, typename _Alloc, _Lock_policy _Lp>
class _Sp_counted_deleter final : public _Sp_counted_base<_Lp> {
    class _Impl : _Sp_ebo_helper<0, _Deleter>, _Sp_ebo_helper<1, _Alloc> {
        typedef _Sp_ebo_helper<0, _Deleter> _Del_base;
        typedef _Sp_ebo_helper<1, _Alloc> _Alloc_base;

    public:
        _Impl(_Ptr __p, _Deleter __d, const _Alloc& __a) noexcept
            : _M_ptr(__p),
              _Del_base(__d),
              _Alloc_base(__a)
        {}

        _Deleter& _M_del() noexcept { return _Del_base::_S_get(*this); }
        _Alloc& _M_alloc() noexcept { return _Alloc_base::_S_get(*this); }

        _Ptr _M_ptr;
    };

public:
    using __allocator_type = __alloc_rebind<_Alloc, _Sp_counted_deleter>;

    // __d(__p) must not throw.
    _Sp_counted_deleter(_Ptr __p, _Deleter __d) noexcept
        : _M_impl(__p, __d, _Alloc()) {}

    // __d(__p) must not throw.
    _Sp_counted_deleter(_Ptr __p, _Deleter __d, const _Alloc& __a) noexcept
        : _M_impl(__p, __d, __a) {}

    ~_Sp_counted_deleter() noexcept {}

    virtual void _M_dispose() noexcept
    {
        _M_impl._M_del()(_M_impl._M_ptr);
    }

    virtual void* _M_get_deleter(const std::type_info& __ti) noexcept
    {
#if __cpp_rtti
        // _GLIBCXX_RESOLVE_LIB_DEFECTS
        // 2400. shared_ptr's get_deleter() should use addressof()
        return __ti == typeid(_Deleter)
            ? std::__addressof(_M_impl._M_del())
            : nullptr;
#else
        return nullptr;
#endif
    }

private:
    _Impl _M_impl;
};
```

可以看出，deleter 和 allocator 直接通过基类“组合”进了 ref-count 块。

**Conclusion**

`shared_ptr<T>` 仅决定 `__shared_ptr::_M_ptr` 的类型，实例的运行时类型和 deleter 均保存在 `_Sp_counted_delter` 中，这个类通过 `__shared_ptr::_M_refcont` 关联。

## How enable_shared_from_this works

回顾一下我们知道，使用 `enable_shared_from_this` 需要满足两个条件：

1. 某个类从 `enable_shared_from_this` 继承
2. 这个类的实例必须要通过 `shared_ptr` 托管。

对于第一点，我们看一下这个类的成员结构（其他的部分对继承几乎没什么影响）：

```cpp
/**
 *  @brief Base class allowing use of member function shared_from_this.
 */
template<typename _Tp>
class enable_shared_from_this {
protected:
    constexpr enable_shared_from_this() noexcept {}

    // Some omitted

private:
    template<typename _Tp1, typename _Tp2>
    friend void __enable_shared_from_this_helper(const __shared_count<>&,
                                                 const enable_shared_from_this<_Tp1>*,
                                                 const _Tp2*) noexcept;

    mutable weak_ptr<_Tp>  _M_weak_this;
};
```

有一个 `weak_ptr` 成员，类型是需要继承的子类，CRTP 的典型用法咯。

还有一个 friend function 后面会提到。

接着看第二点，这里拿 `make_shared()` 的 case 做例子，看一段前面看过的代码：

```cpp
template<typename _Alloc, typename... _Args>
__shared_ptr(_Sp_make_shared_tag __tag, const _Alloc& __a, _Args&&... __args)
    : _M_ptr(),
      _M_refcount(__tag, (_Tp*)0, __a, std::forward<_Args>(__args)...)
{
    // _M_ptr needs to point to the newly constructed object.
    // This relies on _Sp_counted_ptr_inplace::_M_get_deleter.
    void* __p = _M_refcount._M_get_deleter(typeid(__tag));
    _M_ptr = static_cast<_Tp*>(__p);

    // <- attention  here
    __enable_shared_from_this_helper(_M_refcount, _M_ptr, _M_ptr);
}
```

不难看出核心是 `__enable_shared_from_this_helper()` 这个函数，并且：

- 调用这个函数的之前，对象实例和 ref-count 块都已经创建完毕了
- 这个函数就是前面说的那个 friend function，所以这个而函数可以直接修改 `enable_shared_from_this` 里的 `_M_weak_this`。

另外，如果托管对象并不需要这个特性，则函数重载会匹配到

```cpp
template<_Lock_policy _Lp>
inline void __enable_shared_from_this_helper(const __shared_count<_Lp>&, ...) noexcept
{}
```

看一下函数的实现：

```cpp
template<typename _Tp1, typename _Tp2>
inline void __enable_shared_from_this_helper(const __shared_count<>& __pn,
                                             const enable_shared_from_this<_Tp1>* __pe,
                                             const _Tp2* __px) noexcept
{
    if (__pe != nullptr)
        __pe->_M_weak_assign(const_cast<_Tp2*>(__px), __pn);
}

// from class enable_shared_from_this
template<typename _Tp1>
void _M_weak_assign(_Tp1* __p, const __shared_count<>& __n) const noexcept
{
    _M_weak_this._M_assign(__p, __n);
}

template<typename _Tp, _Lock_policy _Lp>
class __weak_ptr {
private:
    // Used by __enable_shared_from_this.
    void _M_assign(_Tp* __ptr, const __shared_count<_Lp>& __refcount) noexcept
    {
        _M_ptr = __ptr;
        _M_refcount = __refcount;
    }

    _Tp*	 	 _M_ptr;         // Contained pointer.
    __weak_count<_Lp>  _M_refcount;    // Reference counter.
};

__weak_count& operator=(const __shared_count<_Lp>& __r) noexcept
{
    _Sp_counted_base<_Lp>* __tmp = __r._M_pi;

    if (__tmp != nullptr)
        __tmp->_M_weak_add_ref();

    if (_M_pi != nullptr)
        _M_pi->_M_weak_release();

    _M_pi = __tmp;

    return *this;
}
```

上面完成了托管对象的父类 `enable_shared_from_this` 中的 `weak_ptr` 和前面通过创建 `shared_ptr` 对象时创建的 ref-count 结构关联。

并且可以看出，完成关联后，weak-count 的计数是2：

1. 父类 enable_shared_from_this 的构造不作任何处理
2. `shared_ptr` 闯将完毕后 usage-count 和 weak-count 都是 1，然后关联的时候通过 `_M_wak_add_ref()` 增加了 weak-count

另外，虽然 `weak_ptr` 的父类是 `__weak_ptr`，但是这个类和 `__shared_ptr` 一样有两个成员，并且 `__weak_count` 的成员也是 `_Sp_counted_base`，和 `__shared_count` 一样。

这里和 MSVC STL 稍有不同，猜测可能是 libstdc++ 为了获取 `shared_ptr` 和 `weak_ptr` 之间更大的独立性？

接下来看一下函数 `shared_from_this()` 的工作原理。

```cpp
// from class enable_shared_from_this

shared_ptr<_Tp> shared_from_this()
{
    return shared_ptr<_Tp>(this->_M_weak_this);
}
```

这里直接利用了从 `weak-ptr` 构造 `shared_ptr` 的技法。

**Conclusion**

这部分的思路和 MSVC STL 一致，`enable_shared_from_this` 中保存一个 `weak_ptr` 成员，并且在构造 `shared_ptr` 对象时和 ref-count 关联。

`shared_from_this()` 直接复用从 `weak-ptr` 构造 `shared_ptr` 的逻辑，只不过这个时候基本不存在 usage count 为 0 的情况。

注：libstdc++ 里有一个类似的类叫 `__enable_shared_from_this`，形式上和 `enable_shared_from_this` 很相似，只不过成员已经直接是 `__weak_ptr`。

至于为什么会有这个 duplication，表示不是很懂。

## How Reference Counting Works

通过前面的分析可以知道，引用计数是由 `__shared_count` 保存的成员指针 `_Sp_counted_base` 管理。

计数 usage-count 和 weak-count 的类型都是 `_Atomic_word`，这个类型定义在 `atomic_word.h` 中，实际上是 `int`。

`_Sp_counted_base` 在构造时将两个计数都设置为 1。

同时提供

- `_M_add_ref_copy()`，`_M_add_ref_lock()` 和 `_M_release()` 由上层根据上下文对 usage-count 进行增减
- `_M_weak_add_ref()` 和 `_M_weak_release()` 由上层根据上下文对 weak-count 进行增减

NOTE：因为 libstdc++ 使用范围更广，所以提供了单线程版本、用锁的版本和使用原子操作的版本。同时因为具体的 atomic 实现在 libstdc++ 里有点乱，这里不研究 atomic operations 的细节，仅假设其能工作正常（废话），并站在高层抽象语义角度去分析对应的原子操作。

先看一下 usage-count 的操作

```cpp
void
_M_add_ref_copy()
{
    __gnu_cxx::__atomic_add_dispatch(&_M_use_count, 1);
}

template<>
inline void
_Sp_counted_base<_S_atomic>::
_M_add_ref_lock()
{
    // Perform lock-free add-if-not-zero operation.
    _Atomic_word __count = _M_get_use_count();
    do
    {
        if (__count == 0)
            __throw_bad_weak_ptr();
        // Replace the current counter value with the old value + 1, as
        // long as it's not changed meanwhile.
    }
    while (!__atomic_compare_exchange_n(&_M_use_count, &__count, __count + 1,
                                        true, __ATOMIC_ACQ_REL,
                                        __ATOMIC_RELAXED));
}

template<>
inline bool
_Sp_counted_base<_S_atomic>::
_M_add_ref_lock_nothrow()
{
    // Perform lock-free add-if-not-zero operation.
    _Atomic_word __count = _M_get_use_count();
    do
    {
        if (__count == 0)
            return false;
        // Replace the current counter value with the old value + 1, as
        // long as it's not changed meanwhile.
    }
    while (!__atomic_compare_exchange_n(&_M_use_count, &__count, __count + 1,
                                        true, __ATOMIC_ACQ_REL,
                                        __ATOMIC_RELAXED));
    return true;
}

void
_M_release() noexcept
{
    // Be race-detector-friendly.  For more info see bits/c++config.
    _GLIBCXX_SYNCHRONIZATION_HAPPENS_BEFORE(&_M_use_count);
    if (__gnu_cxx::__exchange_and_add_dispatch(&_M_use_count, -1) == 1)
    {
        _GLIBCXX_SYNCHRONIZATION_HAPPENS_AFTER(&_M_use_count);
        _M_dispose();
        // There must be a memory barrier between dispose() and destroy()
        // to ensure that the effects of dispose() are observed in the
        // thread that runs destroy().
        // See http://gcc.gnu.org/ml/libstdc++/2005-11/msg00136.html
        if (_Mutex_base<_Lp>::_S_need_barriers)
        {
            _GLIBCXX_READ_MEM_BARRIER;
            _GLIBCXX_WRITE_MEM_BARRIER;
        }

        // Be race-detector-friendly.  For more info see bits/c++config.
        _GLIBCXX_SYNCHRONIZATION_HAPPENS_BEFORE(&_M_weak_count);
        if (__gnu_cxx::__exchange_and_add_dispatch(&_M_weak_count,
                -1) == 1)
        {
            _GLIBCXX_SYNCHRONIZATION_HAPPENS_AFTER(&_M_weak_count);
            _M_destroy();
        }
    }
}
```

`_M_add_ref_copy()` 就是单纯的自增 usage-count，发生在 `__shared_count` 的拷贝创建或者赋值时；而 `_M_add_ref_lock()` 以来一个完整的 CAS，根据之前对 MSVC STL 的分析，我们这里可以猜测这个函数是用来 promote 一个 `weak_ptr` 到 `shared_ptr` 的，后面可以验证一下这个猜想。

`_M_release()` 会首先自减 usage-count，如果减到0了，就释放托管的对象实例；同时再自减 weak-count，如果此时也为 0，那么说明这个 ref-count 已经凉了，就可以整个销毁了。

然后是 weak-count 的计数操作：

```cpp
void
_M_weak_add_ref() noexcept
{
    __gnu_cxx::__atomic_add_dispatch(&_M_weak_count, 1);
}

void
_M_weak_release() noexcept
{
    // Be race-detector-friendly. For more info see bits/c++config.
    _GLIBCXX_SYNCHRONIZATION_HAPPENS_BEFORE(&_M_weak_count);
    if (__gnu_cxx::__exchange_and_add_dispatch(&_M_weak_count, -1) == 1)
    {
        _GLIBCXX_SYNCHRONIZATION_HAPPENS_AFTER(&_M_weak_count);
        if (_Mutex_base<_Lp>::_S_need_barriers)
        {
            // See _M_release(),
            // destroy() must observe results of dispose()
            _GLIBCXX_READ_MEM_BARRIER;
            _GLIBCXX_WRITE_MEM_BARRIER;
        }
        _M_destroy();
    }
}
```

基本和上面一致。

`_M_weak_add_ref()` 的调用时机基本是：

- 从 `__weak_count` 或 `__shared_count` 拷贝构造得到一个 `__weak_count`
- 将一个 `__weak_count` 或 `__shared_count` 赋值给另外一个 `__weak_count` （这种情况伴随着 `_M_weak_release()` 调用）

`_M_weak_release()` 除了赋值时会调用外，剩下的就是析构时。

**Conclusion**

两个引用计数的操作基本转换成了 `__shared_count` 对象和 `__weak_count` 对象之间的交互。

一般情况下，实现采用的方案都是通过 atomic operations 实现计数，但是根据源码我们也发现了专门针对单线程版本和不支持 atomic 的有锁版本。

## How weak_ptr relates with shared_ptr

这部分和上一个 ref-count 的分析部分其实关系非常紧密。

promotion 在外部的表象就是通过 `weak_ptr::lock()` 完成。虽然通过 `shared_ptr` 的构造函数也可以，但是如果对象已经死了，构造函数会抛异常，徒增复杂度，我想应该没谁会这么折腾吧。

```cpp
// from weak_ptr
shared_ptr<_Tp>
lock() const noexcept
{
    return shared_ptr<_Tp>(*this, std::nothrow);
}

// from shared_ptr
// This constructor is non-standard, it is used by weak_ptr::lock().
shared_ptr(const weak_ptr<_Tp>& __r, std::nothrow_t)
    : __shared_ptr<_Tp>(__r, std::nothrow) { }

// from __shared_ptr
// This constructor is used by __weak_ptr::lock() and
// shared_ptr::shared_ptr(const weak_ptr&, std::nothrow_t).
__shared_ptr(const __weak_ptr<_Tp, _Lp>& __r, std::nothrow_t)
    : _M_refcount(__r._M_refcount, std::nothrow)
{
    _M_ptr = _M_refcount._M_get_use_count() ? __r._M_ptr : nullptr;
}

// Now that __weak_count is defined we can define this constructor:
template<_Lock_policy _Lp>
inline
__shared_count<_Lp>::
__shared_count(const __weak_count<_Lp>& __r, std::nothrow_t)
    : _M_pi(__r._M_pi)
{
    if (_M_pi != nullptr)
        if (!_M_pi->_M_add_ref_lock_nothrow())
            _M_pi = nullptr;
}
```

可以看出这里和 MSVC STL 大致上还是类似的。

利用 CAS 去加 usage-count，如果成功了，说明对象还活着，成功 promotion；反之，计数为0，自增函数返回 false，构造一个空对象。


## Thread-safety of shared_ptr Instances

假设对一个 `shared_ptr` 对象 sp 又读又写：

1. 从 sp 创建一个拷贝
2. 将 sp 设置为另一个 `shared_ptr` 实例

这两点涉及 `shared_ptr` 的拷贝构造或拷贝赋值：

```cpp
__shared_ptr(const __shared_ptr&) noexcept = default;
__shared_ptr& operator=(const __shared_ptr&) noexcept = default;
```

直接采用编译器提供的实现。

因为 `__shared_ptr` 存有实例对象指针和计数控制块指针，复制操作做不到原子的改变两个值。

至于多个对象独立的析构，前面我们可以看到，这个是通过原子的改变引用计数完成，可以保证线程安全性。

同时，通过上一节可以看出 `weak_ptr` 到 `shared_ptr` 的 promotion 也是通过 CAS 操作保证了安全性。

## Epilogue

Libstdc++ 的分析版本到这里也结束了。

相比 MSVC STL，我觉得 libstdc++ 版本分析起来更困难，原因总结有三：

1. 源码分析环境不如直接在 Windows 那么友好，一些 gnu-extension 啥的文件需要翻来覆去的找
2. 部分内存分配采用 allocator，我对 allocator 真心不熟悉
  举例：有多少人知道 allocator 一开始不是为了 a mean of allocation 出现，而是为了屏蔽 near/far 地址
3. 因为 libstdc++ 涉及更多的平台，并且有一些扩展实现，增加了代码分析的量