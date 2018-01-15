---
title: Binding to Privately Inherited Member Functions
categories: PROGRAMMING
date: 2018-01-15 18:36:22
tags: [c++, std::bind, private inheritance]
---
考虑代码

<pre class="hljs" style="display: block; overflow-x: auto; padding: 0.5em; background: rgb(51, 51, 51); color: rgb(255, 255, 255);"><span class="hljs-meta" style="color: rgb(252, 155, 155);">#<span class="hljs-meta-keyword">include</span> <span class="hljs-meta-string">&lt;functional&gt;</span></span>

<span class="hljs-keyword" style="color: rgb(252, 194, 140);">class</span> Base {
<span class="hljs-keyword" style="color: rgb(252, 194, 140);">public</span>:
    <span class="hljs-function"><span class="hljs-keyword" style="color: rgb(252, 194, 140);">void</span> <span class="hljs-title" style="color: rgb(255, 255, 170);">Foo</span><span class="hljs-params">(<span class="hljs-keyword" style="color: rgb(252, 194, 140);">int</span> n)</span>
    </span>{
        <span class="hljs-built_in" style="color: rgb(255, 255, 170);">printf</span>(<span class="hljs-string" style="color: rgb(162, 252, 162);">"the value is %d"</span>, n);
    }
};

<span class="hljs-keyword" style="color: rgb(252, 194, 140);">class</span> Derived : Base {
<span class="hljs-keyword" style="color: rgb(252, 194, 140);">public</span>:
    <span class="hljs-keyword" style="color: rgb(252, 194, 140);">using</span> Base::Foo;
};

<span class="hljs-function"><span class="hljs-keyword" style="color: rgb(252, 194, 140);">int</span> <span class="hljs-title" style="color: rgb(255, 255, 170);">main</span><span class="hljs-params">()</span>
</span>{
    <span class="hljs-keyword" style="color: rgb(252, 194, 140);">auto</span> f = <span class="hljs-built_in" style="color: rgb(255, 255, 170);">std</span>::bind(&amp;Derived::Foo, <span class="hljs-built_in" style="color: rgb(255, 255, 170);">std</span>::placeholders::_1, <span class="hljs-number" style="color: rgb(211, 99, 99);">10</span>);
    Derived o;
    f(&amp;o);
    <span class="hljs-keyword" style="color: rgb(252, 194, 140);">return</span> <span class="hljs-number" style="color: rgb(211, 99, 99);">0</span>;
}</pre>

编译会提示出错，错误信息类似：_Failed to specialize function template 'unknown-type std::_Binder<std::_Unforced,void (__cdecl Base::* )(int),const std::_Ph<1> &,int>::operator ()(_Unbound &&...) const'_

根本原因是，即使前面使用了 `using Base::Foo;`, 也只是改变了这个函数在子类中的可见性，影响名字查找；并没有真的在子类中添加了这个函数，因此 `&Derived::Foo` 实际上获取的类型是 `void (Base::*)(int)`，而正是这个类型影响了 `std::bind()` 的实例化。

因此当后面用 `Derived` 的实例去调用 function object 时会失败。

Stackoverflow 上有一个[类似的问题](https://stackoverflow.com/questions/36263159/binding-to-privately-inherited-member-function)，可以参考。

## Workaround

**(1) 子类增加一个 call-wrapper**

然而大多数采用 private-inheritance 而不是 composition 的情况下，都是避免添加各种 call-wrapper

**(2) 绑定 lambda 而不是函数指针**

这是一个推荐的做法

**(3) 强行用 reinterpret_cast<>**

呃... let's don't be evil
