---
title: 浅析 shared_ptr：MSVC STL 篇
categories: PROGRAMMING
date: 2018-03-16 00:52:05
tags: [shared_ptr-internals, source-code-study, shared_ptr, weak_ptr, c++, visual studio]
---
序言请移步[此处](https://kingsamchen.github.io/2018/03/13/shared-ptr-internals-introduction/)

因为这是系列第一篇，所以会带一些功能的 demo，以方便叙述。

## How shared_ptr<T>(new T()) differs from make_shared<T>()

首先考虑 `shared_ptr` 对象的创建，对于给定类型 `T`，假设通过

```c++
auto ptr = std::make_shared<T>(...);
```

创建一个实例。

看一下函数代码：

```c++
template<class _Ty,
         class... _Types>
_NODISCARD inline shared_ptr<_Ty> make_shared(_Types&&... _Args)
{
    // make a shared_ptr
    const auto _Rx = new _Ref_count_obj<_Ty>(_STD forward<_Types>(_Args)...);

    shared_ptr<_Ty> _Ret;
    _Ret._Set_ptr_rep_and_enable_shared(_Rx->_Getptr(), _Rx);
    return (_Ret);
}
```

这里首先在 heap 上创建了一个 `_Ref_count_obj<_Ty>` 对象，通过 `std::forward()` 将 `make_shared()` 的参数转发作为构造函数；接着通过 default contructor 创建了一个 `shared_ptr<_Ty>`，并调用 `_Set_ptr_rep_and_enable_shared()` 设置相关数据。

因为创建 `_Ty` 实例需要的参数 `_Args` 被转发到了 `_Ref_count_obj` 的构造函数中，且 `shared_ptr` 的 default constructor 实质上是一个 _constexpr function_，因此猜测 `shared_ptr` 自身并不负责创建其管理的 object instance，而是将这部分操作“委托”给 `_Ref_count_obj`。

下面看一下 `_Ref_count_obj` 的结构

```c++
class _Ref_count_base {
private:
	virtual void _Destroy() _NOEXCEPT = 0;
	virtual void _Delete_this() _NOEXCEPT = 0;

	// _Atomic_counter_t is actually unsigned long
	_Atomic_counter_t _Uses;
	_Atomic_counter_t _Weaks;
};

template<class _Ty>
class _Ref_count_obj
    : public _Ref_count_base {
    ...
private:
    aligned_union_t<1, _Ty> _Storage;
};
```

引用计数的信息保存在了基类 `_Ref_count_base` 中，并且只是一个普通的 `unsigned long`，估计加减的相关原子操作是直接利用 `Interlocked*` 系列的 API 来完成。

计数相关的细节后面会分析，这里先略过。

`_Ref_count_obj` 自己只有一个成员，不过这个成员很有意思。

`aligned_union_t<1, _Ty>` 会在内部提供一个
- 地址按照 `_Ty` 要求对齐
- 大小为 `max(1, sizeof(_Ty))` 的 buffer

所以 `_Storage` 其实就是一个大小可以容纳一个 `_Ty` 实例，且地址经过对齐的 buffer，并且这个 buffer 就是用来保存 `shared_ptr` 要管理的 `_Ty` 的实例。

这可以结合 `_Ref_count_obj` 的构造函数看出：

```c++
template<class... _Types>
explicit _Ref_count_obj(_Types&&... _Args)
    : _Ref_count_base()
{
    // construct from argument list
    ::new (static_cast<void *>(&_Storage)) _Ty(_STD forward<_Types>(_Args)...);
}

_Ty * _Getptr()
{
    // get pointer
    return (reinterpret_cast<_Ty *>(&_Storage));
}
```

构造函数里通过 *placement new* 在前面说的 buffer 里构造了出了目标实例。

并且，**实例和（计数）控制块实质上是一起存放在一块“大”内存上的**。

至此，`_Rx` 的构造就结束了。

接下来看一下

```c++
shared_ptr<_Ty> _Ret;
_Ret._Set_ptr_rep_and_enable_shared(_Rx->_Getptr(), _Rx);
```

是如何将 `_Rx` 和自己关联上的。

```cpp
// From class shared_ptr
template<class _Ux>
void _Set_ptr_rep_and_enable_shared(_Ux* _Px, _Ref_count_base* _Rx)
{
    // take ownership of _Px
    this->_Set_ptr_rep(_Px, _Rx);
    _Enable_shared_from_this(*this, _Px);
}

template<class _Ty>
class _Ptr_base {
public:
    using element_type = remove_extent_t<_Ty>;

    void _Set_ptr_rep(element_type* _Other_ptr, _Ref_count_base* _Other_rep)
    {
        // take new resource
        _Ptr = _Other_ptr;
        _Rep = _Other_rep;
    }

private:
    element_type* _Ptr {nullptr};
    _Ref_count_base* _Rep {nullptr};
};
```

可以看出，`shared_ptr` 的基类 `_Ptr_base` 有且仅有两个成员，分别是 1) 指向 object instance 的指针 2) 指向 ref-counted 控制块的指针；这两个指针被分别设置为前面 `_Ref_count_obj` 有关的地址。

最后那个 `_Enable_shared_from_this()` 调用和 `std::enable_shared_from_this` 有关，后面会分析，这里先忽略。

到这里，函数 `std::make_shared()` 的整个流程我们已经清楚了，接下来就是研究并且对比一下，直接通过构造函数创建会有什么不同，亦即：

```cpp
std::shared_ptr<T> sp(new T(...));
```

对应调用的构造函数是：

```cpp
template<class _Ux,
    enable_if_t<conjunction_v<conditional_t<is_array_v<_Ty>, _Can_array_delete<_Ux>, _Can_scalar_delete<_Ux>>,
    _SP_convertible<_Ux, _Ty>>, int> = 0>
    explicit shared_ptr(_Ux* _Px)
{	// construct shared_ptr object that owns _Px
    _Setp(_Px, is_array<_Ty>{});
}
```

通过实现我们至少可以发现两点：

1. 没有专门（dedicated）的通过 `_Ty*` 创建对象的构造函数，而且凡是能够 implicit cast 到 `_Ty*` 的 `_Ux*` 都是被支持的
2. C++ 17 开始 `shared_ptr` 能够[支持数组类型](http://en.cppreference.com/w/cpp/memory/shared_ptr/shared_ptr)了。不知道这是个好消息还是坏消息……


由于 C++ 17 还没全面铺开，这里假设当作 C++ 11/14，不考虑类型是数组的 case。

接下来看一下 `_Setp()` 这个函数

```cpp
template <class _Ux>
void _Setp(_Ux* _Px, false_type) {  // take ownership of _Px
  _TRY_BEGIN                        // allocate control block and set
      _Set_ptr_rep_and_enable_shared(_Px, new _Ref_count<_Ux>(_Px));
  _CATCH_ALL  // allocation failed, delete resource
      delete _Px;
  _RERAISE;
  _CATCH_END
}
```

函数 `_Set_ptr_rep_and_enable_shared()` 前面分析过，是用来关联 `shared_ptr` instance 和它的 ref-count 控制块。

这里做了一个异常处理，应该是考虑到 `new _Ref_count<_Ux>(_Px))` 可能会出现异常。

类型 `_Ref_count` 和前面分析过的 `_Ref_count_obj` 一样，都是 `_Ref_count_base` 的子类：

```cpp
// CLASS TEMPLATE _Ref_count
template <class _Ty>
class _Ref_count : public _Ref_count_base {  // handle reference counting for
                                             // pointer without deleter
 public:
  explicit _Ref_count(_Ty* _Px) : _Ref_count_base(), _Ptr(_Px) {  // construct
  }

 private:
  virtual void _Destroy() _NOEXCEPT override {  // destroy managed resource
    delete _Ptr;
  }

  virtual void _Delete_this() _NOEXCEPT override {  // destroy self
    delete this;
  }

  _Ty* _Ptr;
};
```

整个类结构比较简单，只有一个指针成员，指向一开始 heap 上分配的对象。

Conclusion: 通过上面的分析，我们可以看出，两种方式的影响主要在于 `shared_ptr` 内部使用的 ref-count control block。

通过 `make_shared()` 创建，使用的是 `_Ref_count_obj`，内部已经包含了对象实例的内存区域，整体上只有一次内存分配。

而通过构造函数创建，使用的是 `_Ref_count`，内部仅有一个指针引用预先创建的对象；整体上有两次内存分配。

## Why Virtual Dtor is Not Necessary When Deleting From a Base Pointer

通过构造函数创建 `shared_ptr` 对象有一个牛逼的副作用：`shared_ptr` 可以正确地通过基类指针析构整个对象，即使基类没有定义 virtual destructor。

换句话说：

```cpp
struct Base {
    // no virtual here
    ~Base()
    {
        printf("~Base\n");
    }
};

struct Derived : Base {
    ~Derived()
    {
    	printf("~Derived\n");
    }
}

// Would call ~Derived() then ~Base() once sp goes out of scope.
std::shared_ptr<Base> sp(new Derived());
```

这个“特性”目前是 `shared_ptr` 独有的，我们可以通过研究代码来理解为什么可以这样做。

回顾前面分析*从构造函数创建 `shared_ptr`* 的代码，可以发现，从一开始 `shared_ptr` 的构造函数到这里的 `_Ref_count`，所有相关函数都是 template，类型逐层传递保证 `_Ref_count::_Ptr` 是 heap 对象的**实际类型**，这意味着这个 `shared_ptr` 实现了在内部保存了管理对象的实际类型，并且 `_Ref_count::_Destroy()` 是直接对实际类型进行 delete expression。

所以，哪怕基类的析构函数不是 virtual，`sp` 一样能够正确析构。

Note，释放相关的细节因为和引用计数有关，留到后面说。

## How Custom Deleter Works and Why It Is Not Part of the Type

假设我们有一个 custom deleter，我们可以这样使用：

```cpp
class C {};

struct custom_deleter {
    void operator()(C* ptr) const {
        // ...
    }
};

std::shared_ptr<C> sp(new C(), custom_deleter());
```

看一下目标构造函数：

```cpp
template <class _Ux,
          class _Dx,
          enable_if_t<conjunction_v<is_move_constructible<_Dx>,
                                    _Can_call_function_object<_Dx&, _Ux*&>,
                                    _SP_convertible<_Ux, _Ty>>,
                      int> = 0>
shared_ptr(_Ux* _Px, _Dx _Dt) {  // construct with _Px, deleter
  _Setpd(_Px, _STD move(_Dt));
}
```

Aside：由于 deleter 的定位一直是 function object，所以正确的实现是要支持 value semantics 并且控制拷贝的 cost，所以这里直接传值 + move。

这里通过函数 `_Setpd()` 设置对象指针和 deleter。

这个函数是不是觉得似曾相识？前面仅设置对象指针的函数叫 `_Setp()`，因此我们可以大胆猜测，相关的函数序列应该是形如 `_Setxyz()`，并且用 `p` 表示对象指针，`d` 表示 deleter；如果后面要研究 custom allocator，那么就会看到用 `a` 代替 allocator。

继续看一下 `_Setpd()` 函数：

```cpp
template <class _UxptrOrNullptr, class _Dx>
void _Setpd(_UxptrOrNullptr _Px,
            _Dx _Dt) {  // take ownership of _Px, deleter _Dt
  _TRY_BEGIN            // allocate control block and set
      _Set_ptr_rep_and_enable_shared(
          _Px,
          new _Ref_count_resource<_UxptrOrNullptr, _Dx>(_Px, _STD move(_Dt)));
  _CATCH_ALL  // allocation failed, delete resource
      _Dt(_Px);
  _RERAISE;
  _CATCH_END
}
```

整体实现和 `_Setp()` 非常像。不过 ref-count 的数据类型换成了 `_Ref_count_resource`，靠猜都知道它肯定也是 `_Ref_count_base` 的子类。

另外，因为要兼容对象指针为 `nullptr` 的情况，因此这里模板参数将指针类型整体保存了下来，即 `_UxptrOrNullptr`。

```cpp
// CLASS TEMPLATE _Ref_count_resource
template <class _Resource,
          class _Dx>
class _Ref_count_resource
    : public _Ref_count_base {  // handle reference counting for object with
                                // deleter
 public:
  _Ref_count_resource(_Resource _Px, _Dx _Dt)
      : _Ref_count_base(),
        _Mypair(_One_then_variadic_args_t(),
                _STD move(_Dt),
                _Px) {  // construct
  }

  // omit other irrelavent code

  _Compressed_pair<_Dx, _Resource> _Mypair;
};
```

类似的，这里的管理对象的类型 `_Dx` 也是对象的实际类型。

比较有意思的是 `_Compressed_pair`，它是 MSVC utility 内部使用而一个辅助结构，核心就是一个 pair，不过和标准的 `std::pair` 不同，`_Compressed_pair` 做了EBO，亦即：当 `_Dx` 是一个空结构的情况下，压缩成员，只保留一个对象指针。

到这里就可以下结论：(custom) deleter 保存在 `_Ref_count_resource` 中，通过 `_Ptr_base` 的 `_Ref_count_base*` 去引用。因为 `_Ptr_base` 这个基类的存在，使得最外层的 `shared_ptr` 无需通过自身保存 deleter 类型的方式就可以访问到 deleter。

这种 indirection layer 实在是太常见了。

## How enable_shared_from_this works

如果一个类需要在某个成员函数中返回指向自己 (this) 的 `shared_ptr`（常见于需要在成员函数中通过 `std::bind()` 创建一个 function object），那么就需要使用 `std::enable_shared_from_this`。

```cpp
struct SharedThisD : std::enable_shared_from_this<SharedThisD> {
    std::shared_ptr<SharedThisD> getptr() {
        return shared_from_this();
    }
};

auto sp = std::make_shared<SharedThisD>();
auto sp2 = sp.getptr();
```

不过这里要注意的是，对象本身一定要首先是通过 `shared_ptr` 托管的，后面会看到为什么。

首先简单过一下 `enable_shared_from_this` 这个类的基本结构（略去无关代码):

```cpp
// CLASS TEMPLATE enable_shared_from_this
template <class _Ty>
class enable_shared_from_this {  // provide member functions that create
                                 // shared_ptr to this
 public:
  // <-- 记住这个 type alias
  using _Esft_type = enable_shared_from_this;

  // Omit irrelavent code

 protected:
  constexpr enable_shared_from_this() _NOEXCEPT : _Wptr() {  // construct
  }

  enable_shared_from_this(const enable_shared_from_this&) _NOEXCEPT
      : _Wptr() {  // construct (must value-initialize _Wptr)
  }

  enable_shared_from_this& operator=(const enable_shared_from_this&)
      _NOEXCEPT {  // assign (must not change _Wptr)
    return (*this);
  }

  ~enable_shared_from_this() = default;

 private:
  // <-- 注意这个 friend
  template <class _Other, class _Yty>
  friend void _Enable_shared_from_this1(const shared_ptr<_Other>& _This,
                                        _Yty* _Ptr,
                                        true_type);

  mutable weak_ptr<_Ty> _Wptr;
};
```

整个类很简单，default constructor 甚至都是强安全的；但是这里存了一个 `weak_ptr`，所以我们再看一下 `weak_ptr` 的简单结构：

```cpp
// CLASS TEMPLATE weak_ptr
template <class _Ty>
class weak_ptr : public _Ptr_base<
                     _Ty> {  // class for pointer to reference counted resource
 public:
  constexpr weak_ptr() _NOEXCEPT {  // construct empty weak_ptr object
  }

  // Omit irrelavent code
};
```

`weak_ptr` 一样是 `_Ptr_base` 的子类，这点和 `shared_ptr` 同构。

回顾前面第一节 *How shared_ptr\<T>(new T()) differs from make_shared\<T>()* 里我们留了一个点，

```cpp
// From class shared_ptr
template<class _Ux>
void _Set_ptr_rep_and_enable_shared(_Ux* _Px, _Ref_count_base* _Rx)
{
    // take ownership of _Px
    this->_Set_ptr_rep(_Px, _Rx);
    _Enable_shared_from_this(*this, _Px);
}
```

`_Enable_shared_from_this()`，这个函数就是 `enable_shared_from_this` 能够正常运转的核心。

```cpp
template <class _Other, class _Yty>
void _Enable_shared_from_this(const shared_ptr<_Other>& _This,
                              _Yty* _Ptr) {  // possibly enable shared_from_this
  _Enable_shared_from_this1(
      _This, _Ptr,
      _Conjunction_t<negation<is_array<_Other>>, negation<is_volatile<_Yty>>,
                     _Can_enable_shared<_Yty>>{});
}
```

前面我们知道无论管理的对象是否使用 `enable_shared_from_this`，都会调用这个函数，那么为了做到正确区分，这里使用了 SFINAE。

如果不考虑 array 和 volatile 的情况，那么判断核心就是 `_Can_enable_shared`：

```cpp
template <class _Yty, class = void>
struct _Can_enable_shared
    : false_type {  // detect unambiguous and accessible inheritance from
                    // enable_shared_from_this
};

template <class _Yty>
struct _Can_enable_shared<_Yty, void_t<typename _Yty::_Esft_type>>
    : is_convertible<remove_cv_t<_Yty>*,
                     typename _Yty::_Esft_type*>::type {  // is_convertible is
                                                          // necessary to verify
                                                          // unambiguous
                                                          // inheritance
};
```

通过这个 SFINAE 看出，匹配的原则是： `_Esft_type*` （其实就是 `class enable_shared_from_this`）能够 cast 到 `_Yty*`；这个做法其实也是自己实现 `is_derived` 常用的技法。

Aside：这里用了 C++ 17 引入的 `void_t`，部分程度上简化了 SFINAE 的构造。

```cpp
template <class _Other, class _Yty>
void _Enable_shared_from_this1(const shared_ptr<_Other>& _This,
                               _Yty* _Ptr,
                               true_type) {  // enable shared_from_this
  if (_Ptr && _Ptr->_Wptr.expired()) {
    _Ptr->_Wptr = shared_ptr<remove_cv_t<_Yty>>(
        _This, const_cast<remove_cv_t<_Yty>*>(_Ptr));
  }
}

template <class _Other, class _Yty>
void _Enable_shared_from_this1(const shared_ptr<_Other>&,
                               _Yty*,
                               false_type) {  // don't enable shared_from_this
}
```

因为管理实例对象是 `enable_shared_from_this` 的子类，而 `_Enable_shared_from_this1()` 这个函数又是一个 friend（往前翻翻），所以可以直接访问到 `_Wptr`。

接着是非常精彩的一幕：将 `eanble_shared_from_this` 的 `weak_ptr _Wptr` 和前面创建完的整个 `shared_ptr` 关联。

这个关联涉及两个点：

1. 通过 `_This` 和 `_Ptr` aliasing contruct 一个 `shared_ptr`
2. 将 (1) 创建的 `shared_ptr` 转存为 `weak_ptr`

两步的核心代码结合在一起如下：

```cpp
template <class _Ty2>
shared_ptr(const shared_ptr<_Ty2>& _Right, element_type* _Px)
    _NOEXCEPT {  // construct shared_ptr object that aliases _Right
  this->_Alias_construct_from(_Right, _Px);
}

template <class _Ty2>
void _Alias_construct_from(
    const shared_ptr<_Ty2>& _Other,
    element_type* _Px) {  // implement shared_ptr's aliasing ctor
  if (_Other._Rep) {
    _Other._Rep->_Incref();
  }

  _Ptr = _Px;
  _Rep = _Other._Rep;
}

template <class _Ty2>
void _Weakly_construct_from(
    const _Ptr_base<_Ty2>& _Other) {  // implement weak_ptr's ctors
  if (_Other._Rep) {
    _Other._Rep->_Incwref();
  }

  _Ptr = _Other._Ptr;   // <-- 管理对象的指针
  _Rep = _Other._Rep;
}
```

如果画一下

```cpp
auto sp = std::make_shared<SharedThisD>();
```

sp 的结构图，那么大概是这样（部分手绘，忽略渣效果）：

![](/img/enable_shared_from_this_msvc.png)

可以看出，完成构造之后 `enable_shared_from_this` 保存了指向子类的 weak-ptr，加上用来管理的 `shared_ptr`，ref-count 控制块里：
- use-count 是 1
- weak-count 是 2

因为没有修改 use-count，所以不会阻碍实例的正确清理。

Bonus：这里可以想一下，如果 `enable_shared_from_this` 里存的是 `shared_ptr` 会怎么样。

同时，那个 weak-ptr 里（via `_Ptr_base`）保存了子类实例的地址。

在当前的例子里，如果考虑 sp 和 sp 里头存的 `_Ref_count_obj`，那么就有三分实例地址信息。

NOTE：上面几个信息可以很方便的在 VS 里通过调试器检查验证。

接着看一下 `shared_from_this()` 这个函数是怎么实现的：

```cpp
_NODISCARD shared_ptr<_Ty> shared_from_this() {  // return shared_ptr
  return (shared_ptr<_Ty>(_Wptr));
}

_NODISCARD shared_ptr<const _Ty> shared_from_this()
    const {  // return shared_ptr
  return (shared_ptr<const _Ty>(_Wptr));
}
```

可以看出这个函数的核心就是直接通过 `weak_ptr _Wptr` 构造了一个 `shared_ptr` 对象。

Conclusion：概括一下使用 `enable_shared_from_this` 的核心是这个类作为子类之后内部的 `weak_ptr` 保存了对外部实例进行管理的 `shared_ptr` 的引用。

## How Reference Counting Works

终于到了大家都关心的如何实现引用计数的部份。

回顾前面我们知道，`shared_ptr` 内部保存了一个指向引用技术块 `_Ref_count_base` 的指针，而计数相关的实现就是由 `_Ref_count_base` 提供。

现在看一下这个基类的结构：

```cpp
// CLASS _Ref_count_base
class _Ref_count_base {  // common code for reference counting
 private:
  virtual void _Destroy() _NOEXCEPT = 0;
  virtual void _Delete_this() _NOEXCEPT = 0;

  _Atomic_counter_t _Uses;
  _Atomic_counter_t _Weaks;

 protected:
  _Ref_count_base()
      : _Uses(1),
        _Weaks(1)  // non-atomic initializations construct
  {}

 public:
  virtual ~_Ref_count_base() _NOEXCEPT {  // TRANSITION, should be non-virtual
  }

  bool
  _Incref_nz() {  // increment use count if not zero, return true if successful
    for (;;) {    // loop until state is known
#if _USE_INTERLOCKED_REFCOUNTING
      _Atomic_integral_t _Count =
          static_cast<volatile _Atomic_counter_t&>(_Uses);

      if (_Count == 0)
        return (false);

      if (static_cast<_Atomic_integral_t>(_InterlockedCompareExchange(
              reinterpret_cast<volatile long*>(&_Uses), _Count + 1, _Count)) ==
          _Count)
        return (true);

#else  /* _USE_INTERLOCKED_REFCOUNTING */
      _Atomic_integral_t _Count = _Load_atomic_counter(_Uses);

      if (_Count == 0)
        return (false);

      if (_Compare_increment_atomic_counter(_Uses, _Count))
        return (true);
#endif /* _USE_INTERLOCKED_REFCOUNTING */
    }
  }

  void _Incref() {  // increment use count
    _MT_INCR(_Uses);
  }

  void _Incwref() {  // increment weak reference count
    _MT_INCR(_Weaks);
  }

  void _Decref() {  // decrement use count
    if (_MT_DECR(_Uses) ==
        0) {  // destroy managed resource, decrement weak reference count
      _Destroy();
      _Decwref();
    }
  }

  void _Decwref() {  // decrement weak reference count
    if (_MT_DECR(_Weaks) == 0) {
      _Delete_this();
    }
  }

  // Omit irrelavent code.
};
```

类初始化的时候，uses 和 weaks 都是 1；引用计数的增减操作是直接利用 `_InterlockedIncrement()` 和 `_InterlockedDecrement()` 这两个 intrinsic API。

减计数涉及到对象的销毁，因此逻辑会多一些。从上面可以看出，通常时候，uses 和 weaks 的增减是独立的；除了计数递减至 0 时。

（1） 如果 uses 计数递减归零，则会**销毁**管理的对象，同时减少 weaks。

注意，这里的销毁不一定会释放管理对象的资源，具体的操作和子类重写的函数有关。

比如，前面提到的 `_Ref_count_obj` 的 `_Destroy()` 仅仅调用了自己的析构函数，不释放内存，因为对象的内存和控制块在一起。

（2）如果 weaks 计数递减归零，那么说明整块引用计数控制块都没有存在的必要了，于是就可以“自杀”释放了内存了。

考虑到 uses 计数也是存放在技术控制块中，因此可以发现：**当且仅当 weaks 计数归零时，才是真正意义上 `shared_ptr` 对象的释放**。

所以这里可以看出使用 `make_shared()` 创建对象的一个缺点：只要有一个 `weak_ptr` 关联着，哪怕对象实例已经归天了，整块内存还是活得好好的。

接下来可以看一下这几个引用计数的增减函数在何时会被上头调用：

- `_Incref()` 会在“从一个 `shared_ptr` 构造”时被调用，无论是 copy construction 还是 aliasing construction
- `_Decref()` 仅会在 `shared_ptr` 实例析构的时候被调用，不过这里有点意思是的是，`shared_ptr` 调用的是 `_Ptr_base` 自己定义的 `_Decref()`， which internally 调用了真正干活的 `_Decref()`
- `_Incwref()` 仅会在函数 `_Weakly_construct_from()` 中被调用
- `_Decwref()` 会在一个 `weak_ptr` 析构时；或 uses 计数归零时被调用；类似的 `_Ptr_base` 自己也定义了一层 `_Decwref()`

还有一个 `_Incref_nz()` 和 `weak_ptr` 到 `shared_ptr` 的 promotion 有关，后面再分析。

## How weak_ptr relates with shared_ptr

众所周知，一个 `weak_ptr` 可以从一个 `shared_ptr` 构造；同时也可以从一个 `weak_ptr` 提升得到 `shared_ptr`。

先看看第一点。

因为标准规定 `weak_ptr::operator=(const shared_ptr& r)` 等价于 `std::weak_ptr<T>(r).swap(*this)`，所以需要分析的标准接口只有构造函数一个。

```cpp
template <class _Ty2,
          enable_if_t<_SP_pointer_compatible<_Ty2, _Ty>::value, int> = 0>
weak_ptr(const shared_ptr<_Ty2>& _Other)
    _NOEXCEPT {  // construct weak_ptr object for resource owned by _Other
  this->_Weakly_construct_from(_Other);
}

// _Ptr_base
template <class _Ty2>
void _Weakly_construct_from(
    const _Ptr_base<_Ty2>& _Other) {  // implement weak_ptr's ctors
  if (_Other._Rep) {
    _Other._Rep->_Incwref();
  }

  _Ptr = _Other._Ptr;
  _Rep = _Other._Rep;
}
```

复制了一下两个成员，以及底层 weaks 计数。

因为 `weak_ptr` 和 `shared_ptr` 同源（`_Ptr_base`），所以信息交换的时候不需要啥中间商。

接着看一下很重要的 promotion 过程，这部分操作由 `weak_ptr::lock()` 提供：

```cpp
_NODISCARD shared_ptr<_Ty> lock() const _NOEXCEPT {  // convert to shared_ptr
  shared_ptr<_Ty> _Ret;
  (void)_Ret._Construct_from_weak(*this);
  return (_Ret);
}
```

重担又还到了 `shared_ptr::_Construct_from_weak()` 身上。

```cpp
// from _Ptr_base
template <class _Ty2>
bool _Construct_from_weak(const weak_ptr<_Ty2>& _Other) {
  // implement shared_ptr's ctor from weak_ptr, and weak_ptr::lock()
  if (_Other._Rep && _Other._Rep->_Incref_nz()) {
    _Ptr = _Other._Ptr;
    _Rep = _Other._Rep;
    return (true);
  }

  return (false);
}
```

这个时候可以回顾一下前面分析 `weak_ptr` 提到的 `_Incref_nz()` 了。

函数操作很简单，如果 uses 计数为 0，说明管理的对象已经死了，那么这个时候直接返回。

如果对象还在，那么就通过 CAS 对计数进行自增。

为什么这里要用 CAS 而不是简单的 increment，留到后面分析 thread-safe 的时候再说。

Conclusion：说到底其实还是引用计数的事儿，不过因为牵扯到一些线程安全的问题，所以有点说不清道不明。

## Thread-safety of shared_ptr Instances

先抛结论：

1. 多个线程同时读写多个（引用同一个管理对象）`shared_ptr` 实例是线程安全的
2. 多个线程同时读写一个 `shared_ptr` 实例是非线程安全的

且以上仅针对 `shared_ptr` 自身而言，非期管理的对象。

先看 case 2，假设有代码

```cpp
// global
std::shared_ptr<Foo> g_ptr;

// locals
std::shared_ptr<Foo> x;
std::shared_ptr<Foo> y;

x = g_ptr;
g_ptr = y
```

`shared_ptr::operator=` 内部会创建临时对象，内部通过 `_Copy_construct_from()` 完成复制：

```cpp
template <class _Ty2>
void _Copy_construct_from(
    const shared_ptr<_Ty2>&
        _Other) {  // implement shared_ptr's (converting) copy ctor
  if (_Other._Rep) {
    _Other._Rep->_Incref();
  }

  _Ptr = _Other._Ptr;
  _Rep = _Other._Rep;
}
```

从代码可以看出，`_Ptr` 和 `_Rep` 的更新期间， `_Other` 完全可以发生改变，引用计数的增减原子性在这里没有什么卵用。

再考虑 case 1，假设我们有一个 sp 管理一个对象实例，那么符合的情况大抵只有：
1. sp 的某个 `shared_ptr` 的拷贝对象析构了
2. 某个引用 sp 的 `weak_ptr` 通过 `lock()` 获得了一个 `shared_ptr`

对于第一点，析构会调用前面说的 `_Decref()`，对 uses 计数做原子减操作，这一步是安全的。

那么有没有可能存在递减为0之后要执行 `_Destroy()` 的时候和其他打算做递增的操作冲突呢？

答案是不存在。

原因如下：我们已经排除了多个线程对同一个 sp 读写的可能（因为已经论证了这个是非线程安全），那么说明至少存在一个 sp 的拷贝，因此 uses 的计数恒大于等于 2。

第二点也是类似的情况，除了 —— 要考虑 promotion 的时候对象已经消亡了。

因为，`lock()` 当且仅当在 uses 还有效时才会做一次递增，而这种“判断-递增”操作是需要 CAS 才能保证原子性的。

## Epilogue

至此，MSVC STL 版本的 `shared_ptr` 和 `weak_ptr` 几处核心代码就分析完了。

To be continued...