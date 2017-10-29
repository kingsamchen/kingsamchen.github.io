---
title: Emulate Haskell Infix Operator in CPP
categories: PROGRAMMING
date: 2017-10-29 20:03:06
tags: [cpp, haskell, infix operator]
---
Usage at a glance

<pre class="hljs" style="display: block; overflow-x: auto; padding: 0.5em; background: rgb(51, 51, 51); color: rgb(255, 255, 255);"><span class="hljs-meta" style="color: rgb(252, 155, 155);">#<span class="hljs-meta-keyword">include</span> <span class="hljs-meta-string">&lt;iostream&gt;</span></span>
<span class="hljs-meta" style="color: rgb(252, 155, 155);">#<span class="hljs-meta-keyword">include</span> <span class="hljs-meta-string">&lt;vector&gt;</span></span>

<span class="hljs-meta" style="color: rgb(252, 155, 155);">#<span class="hljs-meta-keyword">include</span> <span class="hljs-string" style="color: rgb(162, 252, 162);">"infix_op.h"</span></span>

<span class="hljs-function"><span class="hljs-keyword" style="color: rgb(252, 194, 140);">int</span> <span class="hljs-title" style="color: rgb(255, 255, 170);">main</span><span class="hljs-params">()</span>
</span>{
    <span class="hljs-keyword" style="color: rgb(252, 194, 140);">auto</span> find = Infix([](<span class="hljs-keyword" style="color: rgb(252, 194, 140);">const</span> <span class="hljs-keyword" style="color: rgb(252, 194, 140);">auto</span>&amp; container, <span class="hljs-keyword" style="color: rgb(252, 194, 140);">const</span> <span class="hljs-keyword" style="color: rgb(252, 194, 140);">auto</span>&amp; value) {
        <span class="hljs-keyword" style="color: rgb(252, 194, 140);">return</span> <span class="hljs-built_in" style="color: rgb(255, 255, 170);">std</span>::find(container.begin(), container.end(), value);
    });

    <span class="hljs-keyword" style="color: rgb(252, 194, 140);">auto</span> seq = <span class="hljs-built_in" style="color: rgb(255, 255, 170);">std</span>::<span class="hljs-built_in" style="color: rgb(255, 255, 170);">vector</span>&lt;<span class="hljs-keyword" style="color: rgb(252, 194, 140);">int</span>&gt; { <span class="hljs-number" style="color: rgb(211, 99, 99);">1</span>, <span class="hljs-number" style="color: rgb(211, 99, 99);">3</span>, <span class="hljs-number" style="color: rgb(211, 99, 99);">5</span>, <span class="hljs-number" style="color: rgb(211, 99, 99);">7</span>, <span class="hljs-number" style="color: rgb(211, 99, 99);">9</span> };

    <span class="hljs-keyword" style="color: rgb(252, 194, 140);">bool</span> found = (seq | find | <span class="hljs-number" style="color: rgb(211, 99, 99);">7</span>) != seq.cend();
    <span class="hljs-built_in" style="color: rgb(255, 255, 170);">std</span>::<span class="hljs-built_in" style="color: rgb(255, 255, 170);">cout</span> &lt;&lt; found &lt;&lt; <span class="hljs-built_in" style="color: rgb(255, 255, 170);">std</span>::<span class="hljs-built_in" style="color: rgb(255, 255, 170);">endl</span>;

    <span class="hljs-keyword" style="color: rgb(252, 194, 140);">return</span> <span class="hljs-number" style="color: rgb(211, 99, 99);">0</span>;
}</pre>

Implementation:

<pre class="hljs" style="display: block; overflow-x: auto; padding: 0.5em; background: rgb(51, 51, 51); color: rgb(255, 255, 255);"><span class="hljs-meta" style="color: rgb(252, 155, 155);">#<span class="hljs-meta-keyword">pragma</span> once</span>

<span class="hljs-meta" style="color: rgb(252, 155, 155);">#<span class="hljs-meta-keyword">include</span> <span class="hljs-meta-string">&lt;xutility&gt;</span></span>

<span class="hljs-keyword" style="color: rgb(252, 194, 140);">template</span>&lt;<span class="hljs-keyword" style="color: rgb(252, 194, 140);">typename</span> BinaryFn&gt;
<span class="hljs-keyword" style="color: rgb(252, 194, 140);">struct</span> InfixOp {
    <span class="hljs-function"><span class="hljs-keyword" style="color: rgb(252, 194, 140);">explicit</span> <span class="hljs-title" style="color: rgb(255, 255, 170);">InfixOp</span><span class="hljs-params">(BinaryFn&amp;&amp; fn)</span>
        : <span class="hljs-title" style="color: rgb(255, 255, 170);">fn_</span><span class="hljs-params">(<span class="hljs-built_in" style="color: rgb(255, 255, 170);">std</span>::forward&lt;BinaryFn&gt;(fn)</span>)
    </span>{}

    BinaryFn fn_;
};

<span class="hljs-keyword" style="color: rgb(252, 194, 140);">template</span>&lt;<span class="hljs-keyword" style="color: rgb(252, 194, 140);">typename</span> BinaryFn&gt;
InfixOp&lt;BinaryFn&gt; Infix(BinaryFn&amp;&amp; fn)
{
    <span class="hljs-keyword" style="color: rgb(252, 194, 140);">return</span> InfixOp&lt;BinaryFn&gt;(<span class="hljs-built_in" style="color: rgb(255, 255, 170);">std</span>::forward&lt;BinaryFn&gt;(fn));
}

<span class="hljs-keyword" style="color: rgb(252, 194, 140);">template</span>&lt;<span class="hljs-keyword" style="color: rgb(252, 194, 140);">typename</span> T, <span class="hljs-keyword" style="color: rgb(252, 194, 140);">typename</span> BinaryFn&gt;
<span class="hljs-keyword" style="color: rgb(252, 194, 140);">struct</span> InfixExpr {
    InfixExpr(T&amp;&amp; lhs, BinaryFn&amp;&amp; op)
        : lhs_(<span class="hljs-built_in" style="color: rgb(255, 255, 170);">std</span>::forward&lt;T&gt;(lhs)), op_(<span class="hljs-built_in" style="color: rgb(255, 255, 170);">std</span>::forward&lt;BinaryFn&gt;(op))
    {}

    <span class="hljs-keyword" style="color: rgb(252, 194, 140);">template</span>&lt;<span class="hljs-keyword" style="color: rgb(252, 194, 140);">typename</span> U&gt;
    <span class="hljs-keyword" style="color: rgb(252, 194, 140);">decltype</span>(<span class="hljs-keyword" style="color: rgb(252, 194, 140);">auto</span>) <span class="hljs-keyword" style="color: rgb(252, 194, 140);">operator</span>|(U&amp;&amp; rhs)
    {
        <span class="hljs-keyword" style="color: rgb(252, 194, 140);">return</span> op_(lhs_, <span class="hljs-built_in" style="color: rgb(255, 255, 170);">std</span>::forward&lt;U&gt;(rhs));
    }

    T lhs_;
    BinaryFn op_;
};

<span class="hljs-keyword" style="color: rgb(252, 194, 140);">template</span>&lt;<span class="hljs-keyword" style="color: rgb(252, 194, 140);">typename</span> T, <span class="hljs-keyword" style="color: rgb(252, 194, 140);">typename</span> BinaryFn&gt;
InfixExpr&lt;T, BinaryFn&gt; <span class="hljs-keyword" style="color: rgb(252, 194, 140);">operator</span>|(T&amp;&amp; lhs, InfixOp&lt;BinaryFn&gt; op)
{
    <span class="hljs-keyword" style="color: rgb(252, 194, 140);">return</span> InfixExpr&lt;T, BinaryFn&gt;(<span class="hljs-built_in" style="color: rgb(255, 255, 170);">std</span>::forward&lt;T&gt;(lhs), <span class="hljs-built_in" style="color: rgb(255, 255, 170);">std</span>::move(op.fn_));
}</pre>

Have fun.
