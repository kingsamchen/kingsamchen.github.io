---
title: 浅析 shared_ptr：Boost 篇
categories: PROGRAMMING
date: 2018-05-02 11:58:19
tags: [shared_ptr-internals, source-code-study, shared_ptr, weak_ptr, c++, boost]
---
序言请移步[此处](https://kingsamchen.github.io/2018/03/13/shared-ptr-internals-introduction/)

MSVC STL 的分析版本请移步[此处](https://kingsamchen.github.io/2018/03/16/demystify-shared-ptr-and-weak-ptr-in-msvc-stl/)

Libstdc++ 的分析版本请移步[此处](https://kingsamchen.github.io/2018/03/30/demystify-shared-ptr-and-weak-ptr-in-libstdcpp/)

注：因为这不是第一篇分析，所以会直入主题，跳过文学写作常用的累赘的过渡。

## 目标版本选择

选用最新的 Boost 1.67 作为研究目标

在开始正题前，先简单看一下 `shared_ptr` 的类成员，方便后续分析：

```cpp
namespace detail {

template< class T > struct sp_element
{
    typedef T type;
};

}

template<class T>
class shared_ptr {
public:
    typedef typename boost::detail::sp_element< T >::type element_type;

    // omitted other

private:
    element_type * px;                 // contained pointer
    boost::detail::shared_count pn;    // reference counter
};
```

## How shared_ptr(new T()) differs from make_shared()

同样的，先看 `make_shared()`，位于 `boost/smart_ptr/make_shared_object.hpp`

```cpp
template< class T, class... Args > typename boost::detail::sp_if_not_array< T >::type make_shared( Args && ... args )
{
    boost::shared_ptr< T > pt( static_cast< T* >( 0 ), BOOST_SP_MSD( T ) );

    boost::detail::sp_ms_deleter< T > * pd = static_cast<boost::detail::sp_ms_deleter< T > *>( pt._internal_get_untyped_deleter() );

    void * pv = pd->address();

    ::new( pv ) T( boost::detail::sp_forward<Args>( args )... );
    pd->set_initialized();

    T * pt2 = static_cast< T* >( pv );

    boost::detail::sp_enable_shared_from_this( &pt, pt2, pt2 );

    return boost::shared_ptr< T >( pt, pt2 );
}
```

这部分代码罕见的异常复杂...

首先创建了一个空 `shared_ptr` 实例 `pt`，调用的是构造函数

```cpp
template<class D>
shared_ptr( boost::detail::sp_nullptr_t p, D d ): px( p ), pn( p, d )
{}
```

因为 `p` 已经是 nullptr，因此这部分的核心是利用自定义的 deleter `BOOST_SP_MSD(T)` 来初始化计数控制块 `pn`。

宏 `BOOST_SP_MSD(T)` 的定义是

```cpp
# define BOOST_SP_MSD( T ) boost::detail::sp_inplace_tag< boost::detail::sp_ms_deleter< T > >()

template< class D > struct sp_inplace_tag
{};
```

`boost::detail::shared_count` 这个我们之前在分析 libstdc++ 版本的代码里也有遇到过。这个类的成员很简单：

```cpp
class shared_count
{
private:

    sp_counted_base * pi_;

#if defined(BOOST_SP_ENABLE_DEBUG_HOOKS)
    int id_;
#endif

    // omitted
};
```

接下来看一下 `boost::detail::shared_count` 的构造相关的过程。

```cpp
template< class P, class D > shared_count( P p, sp_inplace_tag<D> ): pi_( 0 )
#if defined(BOOST_SP_ENABLE_DEBUG_HOOKS)
        , id_(shared_count_id)
#endif
{
    try
    {
        // Note: extract deleter type D and use its default contructor.
        pi_ = new sp_counted_impl_pd< P, D >( p );
    }
    catch( ... )
    {
        D::operator_fn( p ); // delete p
        throw;
    }
}

template<class P, class D> class sp_counted_impl_pd: public sp_counted_base
{
private:

    P ptr; // copy constructor must not throw
    D del; // copy constructor must not throw

    sp_counted_impl_pd( sp_counted_impl_pd const & );
    sp_counted_impl_pd & operator= ( sp_counted_impl_pd const & );

    typedef sp_counted_impl_pd<P, D> this_type;

public:

    // pre: d(p) must not throw

    sp_counted_impl_pd( P p, D & d ): ptr( p ), del( d )
    {
    }

    // Deleter is default constructed.
    sp_counted_impl_pd( P p ): ptr( p ), del()
    {
    }
};
```

可以看出，前面一顿操作猛如虎（`shared_ptr` -> `shared_count` -> `sp_counted_impl_pd`），最后的结果就是创建了一个默认构造的 `boost::detail::sp_ms_deleter<T>`，于是我们得看一下 `sp_ms_deleter` 这类的结构和几个相关的操作：

```cpp
template< class T > class sp_ms_deleter
{
private:

    typedef typename sp_aligned_storage< sizeof( T ), ::boost::alignment_of< T >::value >::type storage_type;

    bool initialized_;

    // NOTE: storage for managed data in type T
    storage_type storage_;

private:

    void destroy() BOOST_SP_NOEXCEPT
    {
        if( initialized_ )
        {
#if defined( __GNUC__ )

            // fixes incorrect aliasing warning
            T * p = reinterpret_cast< T* >( storage_.data_ );
            p->~T();

#else

            reinterpret_cast< T* >( storage_.data_ )->~T();

#endif

            initialized_ = false;
        }
    }

public:

    sp_ms_deleter() BOOST_SP_NOEXCEPT : initialized_( false )
    {
    }

    template<class A> explicit sp_ms_deleter( A const & ) BOOST_SP_NOEXCEPT : initialized_( false )
    {
    }

    // optimization: do not copy storage_
    sp_ms_deleter( sp_ms_deleter const & ) BOOST_SP_NOEXCEPT : initialized_( false )
    {
    }

    ~sp_ms_deleter() BOOST_SP_NOEXCEPT
    {
        destroy();
    }

    void operator()( T * ) BOOST_SP_NOEXCEPT
    {
        destroy();
    }

    static void operator_fn( T* ) BOOST_SP_NOEXCEPT // operator() can't be static
    {
    }

    void * address() BOOST_SP_NOEXCEPT
    {
        return storage_.data_;
    }

    void set_initialized() BOOST_SP_NOEXCEPT
    {
        initialized_ = true;
    }
};
```

这里保存管理对象的方式和 MSVC STL 一样，都是使用了 `aligned_storage` 直接在构造 deleter 对象时一同分配好对象的存储空间。

剩下的就是让外部访问到这块空间的地址，并利用 `placement new` 进行对象的构造；这也就是上面这几段代码做的事情。

另外注意这个类的基类，`sp_counted_base` 保存了引用计数成员。

接下来的 enable-shared-from-this 是常规操作，暂时先跳过，看一下最后一句：

```cpp
return boost::shared_ptr< T >( pt, pt2 );
```

前面虽然在控制块里构造出了管理对象，但是 `shared_ptr` 以及内部的 `sp_counted_impl_pd` 指向管理对象的指针都是空的，所以在返回之前，要做一次“矫正”；也就是这里重新构建一个 `shared_ptr` 的对象。

```cpp
template< class Y >
shared_ptr( shared_ptr<Y> const & r, element_type * p ) BOOST_SP_NOEXCEPT : px( p ), pn( r.pn )
{}

shared_count(shared_count const & r): pi_(r.pi_) // nothrow
#if defined(BOOST_SP_ENABLE_DEBUG_HOOKS)
        , id_(shared_count_id)
#endif
{
    if( pi_ != 0 ) pi_->add_ref_copy();
}
```

有一个点需要注意，前面 `sp_counted_impl_pd::ptr` 这里一直是空的，并没有设置，因为这种 case 下，这个成员有点冗余。

注：这里可以对比 MSVC 和 libstdc++ 的实现。

接下来看一下直接从一个已创建的对象构造的流程，这块基本就是一气呵成了：

```cpp
template<class Y>
explicit shared_ptr( Y * p ): px( p ), pn() // Y must be complete
{
    boost::detail::sp_pointer_construct( this, p, pn );
}

template< class T, class Y >
inline void sp_pointer_construct( boost::shared_ptr< T > * ppx, Y * p, boost::detail::shared_count & pn )
{
    boost::detail::shared_count( p ).swap( pn );
    boost::detail::sp_enable_shared_from_this( ppx, p, p );
}

template<class Y> explicit shared_count( Y * p ): pi_( 0 )
#if defined(BOOST_SP_ENABLE_DEBUG_HOOKS)
        , id_(shared_count_id)
#endif
{
    try
    {
        pi_ = new sp_counted_impl_p<Y>( p );
    }
    catch(...)
    {
        boost::checked_delete( p );
        throw;
    }
};

template<class X> class sp_counted_impl_p: public sp_counted_base
{
private:

    X * px_;

    sp_counted_impl_p( sp_counted_impl_p const & );
    sp_counted_impl_p & operator= ( sp_counted_impl_p const & );

    typedef sp_counted_impl_p<X> this_type;

public:

    explicit sp_counted_impl_p( X * px ): px_( px )
    {
#if defined(BOOST_SP_ENABLE_DEBUG_HOOKS)
        boost::sp_scalar_constructor_hook( px, sizeof(X), this );
#endif
    }
};
```

可以看出这个场合下用的类是 `sp_counted_impl_p`，内部只包含了一个 `X* px_`

**Conclusion**

使用 `make_shared()` 在这里仍然保证引用计数控制块和托管对象使用同一块内存。

Boost 的实现也引入了 `shared_count` 这一中间层，但是在内存分配上没怎么使用 allocator。

## Why Virtual Dtor is Not Necessary When Deleting From a Base Pointer

这点只需要回顾一下上面直接通过 ptr 构造 `shared_ptr` 的代码就可以发现，从构造函数开始，就是通过一个单独的 `template <class Y>` 将指针的具体类型逐层传递，一直到 `sp_counted_impl_p` 中。

## How Custom Deleter Works and Why It Is Not Part of the Type

从构造函数开始跟

```cpp
template<class Y, class D> shared_ptr( Y * p, D d ): px( p ), pn( p, d )
{
    boost::detail::sp_deleter_construct( this, p );
}
```

因为 custom deleter 和 `sp_deleter_construct()` 无关，所以这里直接看 `pn` 的构造。

```cpp
template<class P, class D> shared_count( P p, D d ): pi_(0)
#if defined(BOOST_SP_ENABLE_DEBUG_HOOKS)
    , id_(shared_count_id)
#endif
{
    try
    {
        pi_ = new sp_counted_impl_pd<P, D>(p, d);
    }
    catch(...)
    {
        d(p); // delete p
        throw;
    }
}
```

`sp_conted_impl_pd` 这个类我们最开始分析 `make_shared()` 的时候已经遇到了。

这里看出，custom deleter 的类型和实际的值都是在构造的时候一层一层传递，直到底层的 ref-counted 块存储。

## How enable_shared_from_this works

回顾一下使用 `enable_shared_from_this` 的两个条件：
1. 从 `enable_shared_from_this` 继承
2. 实例用 `shared_ptr` 托管

首先看一下这个类的成员结构：

```cpp
template<class T> class enable_shared_from_this
{
protected:

    BOOST_CONSTEXPR enable_shared_from_this() BOOST_SP_NOEXCEPT
    {

    ~enable_shared_from_this() BOOST_SP_NOEXCEPT // ~weak_ptr<T> newer throws, so this call also must not throw
    {}

private:
    mutable weak_ptr<T> weak_this_;
};

```

类似的做法，内部包含了一个 weak_ptr；简单看一下 weak_ptr 的结构：

```cpp
template<class T> class enable_shared_from_this
{
protected:

    BOOST_CONSTEXPR enable_shared_from_this() BOOST_SP_NOEXCEPT
    {

    ~enable_shared_from_this() BOOST_SP_NOEXCEPT // ~weak_ptr<T> newer throws, so this call also must not throw
    {}

private:
    mutable weak_ptr<T> weak_this_;
};

template<class T> class weak_ptr
{
private:

    // Borland 5.5.1 specific workarounds
    typedef weak_ptr<T> this_type;

public:

    typedef typename boost::detail::sp_element< T >::type element_type;

    BOOST_CONSTEXPR weak_ptr() BOOST_SP_NOEXCEPT : px(0), pn()
    {
    }

private:

    template<class Y> friend class weak_ptr;
    template<class Y> friend class shared_ptr;

    element_type * px;            // contained pointer
    boost::detail::weak_count pn; // reference counter

};  // weak_ptr


class weak_count
{
private:
    // <-- same as which in shared_count
    sp_counted_base * pi_;

#if defined(BOOST_SP_ENABLE_DEBUG_HOOKS)
    int id_;
#endif

    friend class shared_count;
};
```

和 MSVC 以及 libstdc++ 都不同，Boost 的 `weak_ptr` 并没有任何基类，不过和 libstdc++ 类似，`weak_ptr` 内部保存一个专门的 `weak_count`，其内部的计数控制块倒是和 `shared_ptr` 是复用的。

翻完了前面这么多介绍性的内容之后，我们挑 `make_shared()` 的用法作为具体例子分析。

回顾一下前面频繁提到的：

```cpp
boost::detail::sp_enable_shared_from_this( &pt, pt2, pt2 );
```

因为前面我们知道被托管的类会从 `enable_shared_from_this` 继承，因此 `pt2` 是可以 cast 到 `enable_shared_from_this<T>`。

```cpp
template< class X, class Y, class T > inline void sp_enable_shared_from_this( boost::shared_ptr<X> const * ppx, Y const * py, boost::enable_shared_from_this< T > const * pe )
{
    if( pe != 0 )
    {
        pe->_internal_accept_owner( ppx, const_cast< Y* >( py ) );
    }
}

// Note: invoked automatically by shared_ptr; do not call
template<class X, class Y> void _internal_accept_owner( shared_ptr<X> const * ppx, Y * py ) const BOOST_SP_NOEXCEPT
{
    if( weak_this_.expired() )
    {
        weak_this_ = shared_ptr<T>( *ppx, py );
    }
}
```

这部分逻辑和 MSVC 的实现反而有点像。

经过这部分初始化之后，基类 `enable_shared_from_this` 内部的 `weak_ptr` 已经指向了正确的数据部分。

接下来看一下 `shared_from_this()` 的实现：

```cpp
shared_ptr<T> shared_from_this()
{
    shared_ptr<T> p( weak_this_ );
    BOOST_ASSERT( p.get() == this );
    return p;
}
```

一样的核心逻辑，从内部保存的 `weak_ptr` 中创建 `shared_ptr`。

## How Reference Counting Works

引用计数的相关操作由 `shared_count` 内部使用的 `sp_counted_base` 决定。

在比较新的环境下，boost 直接使用标准库的 atomic 设施作为引用计数

```cpp
class sp_counted_base
{
private:

    sp_counted_base( sp_counted_base const & );
    sp_counted_base & operator= ( sp_counted_base const & );

    std::atomic_int_least32_t use_count_;   // #shared
    std::atomic_int_least32_t weak_count_;  // #weak + (#shared != 0)
};
```

而引用计数的相关操作也封装了一层薄薄的 utils wrapper

```cpp
inline void atomic_increment( std::atomic_int_least32_t * pw )
{
    pw->fetch_add( 1, std::memory_order_relaxed );
}

inline std::int_least32_t atomic_decrement( std::atomic_int_least32_t * pw )
{
    return pw->fetch_sub( 1, std::memory_order_acq_rel );
}

inline std::int_least32_t atomic_conditional_increment( std::atomic_int_least32_t * pw )
{
    // long r = *pw;
    // if( r != 0 ) ++*pw;
    // return r;

    std::int_least32_t r = pw->load( std::memory_order_relaxed );

    for( ;; )
    {
        if( r == 0 )
        {
            return r;
        }

        if( pw->compare_exchange_weak( r, r + 1, std::memory_order_relaxed, std::memory_order_relaxed ) )
        {
            return r;
        }
    }
}
```

这里几个操作选用的 memory ordering 有点意思，个人不是很明确这么选区的意思，但是猜想可能是如下原因：
- increment 相关的操作只需要保证值的原子性即可，因为没有新值的要求
- decrement 需要获取操作前的值（via `fetch_sub()`），判断是否应该开始进行清理了，所以需要保证这个点之前的操作对后续的可见性
- conditional increment 同理

一个佐证是，`usage_count()` 会使用 acquire ordering，因为他确实要保证这个值的一致性。

```cpp
long use_count() const // nothrow
{
    return use_count_.load( std::memory_order_acquire );
}
```

另外类似的，use-count 递减至0后，仅会引起对象的析构，仅当 weak-count 为0时，整个控制块内存才会被释放。

注：如果打开 boost 的目录，会发现 boost 针对不同的环境和平台，定制了一系列的 `sp_counted_base` 实现，以尽可能提供最适合的计数控制。

## How weak_ptr relates with shared_ptr

追踪一下 `weak_ptr::lock()` 的实现

```cpp
shared_ptr<T> lock() const BOOST_SP_NOEXCEPT
{
    return shared_ptr<T>( *this, boost::detail::sp_nothrow_tag() );
}

template<class Y>
shared_ptr( weak_ptr<Y> const & r, boost::detail::sp_nothrow_tag )
BOOST_SP_NOEXCEPT : px( 0 ), pn( r.pn, boost::detail::sp_nothrow_tag() )
{
    if( !pn.empty() )
    {
        px = r.px;
    }
}

inline shared_count::shared_count( weak_count const & r, sp_nothrow_tag ): pi_( r.pi_ )
#if defined(BOOST_SP_ENABLE_DEBUG_HOOKS)
        , id_(shared_count_id)
#endif
{
    if( pi_ != 0 && !pi_->add_ref_lock() )
    {
        pi_ = 0;
    }
}

bool add_ref_lock() // true on success
{
    return atomic_conditional_increment( &use_count_ ) != 0;
}
```

其中 `atomic_condition_increment()` 的实现在上一个话题分析过了。

## Thread-safety of shared_ptr Instances

通过上面的分析，我们知道，哪怕是在 boost 里，一个 `shared_ptr` 也有 `T* px` 和 `shared_count pn` 两个成员。

因此多个线程同时读写一个 `shared_ptr` 是非线程安全。

又因为上面也可以看到，引用计数的增减是通过 `std::atomic` 达到，所以同时对多个同一引用的 `shared_ptr` 操作时线程安全的。

而 `weak_ptr` 到 `shared_ptr` 的 promotion 也通过 CAS 保证了安全性。

## Epilogue

Boost 版本的代码分析到这里就结束了。

这个版本其实分析的时候是蛋疼的，因为干扰代码太多（毕竟 boost 要兼容各种版本的编译器和各种神奇的平台），同时通过 vcpkg 安装 boost 的时候被 GFW 恶心的不行。

同时，因为这个版本和 libstdc++ 比较像，而且等于是第三遍分析，所以很多地方都分析的并不是那么细致...