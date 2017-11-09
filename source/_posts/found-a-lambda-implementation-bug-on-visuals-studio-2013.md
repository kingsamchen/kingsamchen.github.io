---
title: 发现一个（疑似）VS 2013 lambda 实现的 Bug
categories: PROGRAMMING
date: 2017-11-09 22:56:25
tags: [c++, visual studio, lambda, bug]
---
考虑如下代码段：

<pre class="hljs" style="display: block; overflow-x: auto; padding: 0.5em; background: rgb(51, 51, 51); color: rgb(255, 255, 255);"><span class="hljs-meta" style="color: rgb(252, 155, 155);">#<span class="hljs-meta-keyword">include</span> <span class="hljs-meta-string">&lt;iostream&gt;</span></span>

<span class="hljs-keyword" style="color: rgb(252, 194, 140);">class</span> Foo {
<span class="hljs-keyword" style="color: rgb(252, 194, 140);">public</span>:
    <span class="hljs-function"><span class="hljs-keyword" style="color: rgb(252, 194, 140);">void</span> <span class="hljs-title" style="color: rgb(255, 255, 170);">Thunk</span><span class="hljs-params">()</span>
    </span>{
        <span class="hljs-keyword" style="color: rgb(252, 194, 140);">auto</span> lambda = [](Foo* f, <span class="hljs-keyword" style="color: rgb(252, 194, 140);">const</span> <span class="hljs-keyword" style="color: rgb(252, 194, 140);">char</span>* msg) {
            <span class="hljs-keyword" style="color: rgb(252, 194, 140);">auto</span> pfn = &amp;Foo::Print;  <span class="hljs-comment" style="color: rgb(136, 136, 136);">// complained this statement</span>
            (*f.*pfn)(msg);
        };

        lambda(<span class="hljs-keyword" style="color: rgb(252, 194, 140);">this</span>, <span class="hljs-string" style="color: rgb(162, 252, 162);">"abc"</span>);
    }

    <span class="hljs-function"><span class="hljs-keyword" style="color: rgb(252, 194, 140);">void</span> <span class="hljs-title" style="color: rgb(255, 255, 170);">Print</span><span class="hljs-params">(<span class="hljs-keyword" style="color: rgb(252, 194, 140);">const</span> <span class="hljs-keyword" style="color: rgb(252, 194, 140);">char</span>* msg)</span>
    </span>{
        <span class="hljs-built_in" style="color: rgb(255, 255, 170);">std</span>::<span class="hljs-built_in" style="color: rgb(255, 255, 170);">cout</span> &lt;&lt; msg &lt;&lt; <span class="hljs-built_in" style="color: rgb(255, 255, 170);">std</span>::<span class="hljs-built_in" style="color: rgb(255, 255, 170);">endl</span>;
    }
};

<span class="hljs-function"><span class="hljs-keyword" style="color: rgb(252, 194, 140);">int</span> <span class="hljs-title" style="color: rgb(255, 255, 170);">main</span><span class="hljs-params">()</span>
</span>{
    Foo foo;

    foo.Thunk();

    <span class="hljs-keyword" style="color: rgb(252, 194, 140);">return</span> <span class="hljs-number" style="color: rgb(211, 99, 99);">0</span>;
}</pre>

在 Visual Studio 2013 with update 5 上会提示编译警告：

"*Line 16: warning C4573: the usage of 'Foo::Print' requires the compiler to capture 'this' but the current default capture mode does not allow it*"

然而相同的代码在 Visual Studio 2017 上，以及 [clang 3.8](https://wandbox.org/permlink/F9Y82SFLZku2FwBZ) 上均不会出现类似警告。

我在 StackOverflow 上提了[一个问题](https://stackoverflow.com/questions/47177263/why-get-pointer-to-member-function-inside-lambda-triggers-warning-on-vs2013)，然而截止本文发布前，并没有看起来靠谱的答案出现。