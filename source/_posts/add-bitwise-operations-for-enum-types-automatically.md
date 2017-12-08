---
title: 自动为 enum 类型添加位运算操作符
categories: PROGRAMMING
date: 2017-12-08 21:43:06
tags: [c++, enum, flag, bitwise， SFINAE]
---
有时候需要在 C++ 里用 `enum (class)` 表示 flags，进行基础的 bitwise 运算，而哪怕是支持自动到 underlying integer 转换的 traditional enum，也需要额外的 cast 才能实现，所以在需要的时候为 `enum` 添加位运算支持还是有一定市场的。

单独为每个需要的 `enum` 提供相关重载可以实现细粒度的控制，并且必要的时候可以加上 range-check 的校验。

然而这里有两个问题：
1. 大部分情况下不需要 range-check 这种检查，因为通常不会在接口这一层暴露
2. 如果一个 module 里会用到两个以上的 enum-based-flags，那么单独实现会很蛋疼

所以一个解决方案是，提供基于函数模板的重载，并且利用 ADL 将这部分重载锁在一个具体的 namespace 内，例如马上（C++ 20）就要被废弃的 `rel_ops`。

同时，我们希望这部分重载只对 `enum / enum class` 有效，其他类型不进行应用。可以通过 SFINAE 实现这点。

利用快下班的时间做了一个基本的实现：

<pre class="hljs" style="display: block; overflow-x: auto; padding: 0.5em; background: rgb(51, 51, 51); color: rgb(255, 255, 255);"><span class="hljs-comment" style="color: rgb(136, 136, 136);">// Casts an enum value into an equivalent integer.</span>
<span class="hljs-keyword" style="color: rgb(252, 194, 140);">template</span>&lt;<span class="hljs-keyword" style="color: rgb(252, 194, 140);">typename</span> E&gt;
<span class="hljs-function"><span class="hljs-keyword" style="color: rgb(252, 194, 140);">constexpr</span> <span class="hljs-keyword" style="color: rgb(252, 194, 140);">auto</span> <span class="hljs-title" style="color: rgb(255, 255, 170);">enum_cast</span><span class="hljs-params">(E e)</span> <span class="hljs-keyword" style="color: rgb(252, 194, 140);">noexcept</span>
</span>{
    <span class="hljs-keyword" style="color: rgb(252, 194, 140);">return</span> <span class="hljs-keyword" style="color: rgb(252, 194, 140);">static_cast</span>&lt;<span class="hljs-built_in" style="color: rgb(255, 255, 170);">std</span>::<span class="hljs-keyword" style="color: rgb(252, 194, 140);">underlying_type_t</span>&lt;E&gt;&gt;(e);
}

<span class="hljs-keyword" style="color: rgb(252, 194, 140);">namespace</span> enum_ops {

<span class="hljs-keyword" style="color: rgb(252, 194, 140);">template</span>&lt;<span class="hljs-keyword" style="color: rgb(252, 194, 140);">typename</span> E&gt;
<span class="hljs-keyword" style="color: rgb(252, 194, 140);">constexpr</span> <span class="hljs-built_in" style="color: rgb(255, 255, 170);">std</span>::<span class="hljs-keyword" style="color: rgb(252, 194, 140);">enable_if_t</span>&lt;<span class="hljs-built_in" style="color: rgb(255, 255, 170);">std</span>::is_enum&lt;E&gt;::value, E&gt; <span class="hljs-keyword" style="color: rgb(252, 194, 140);">operator</span>|(E lhs, E rhs) <span class="hljs-keyword" style="color: rgb(252, 194, 140);">noexcept</span>
{
    <span class="hljs-keyword" style="color: rgb(252, 194, 140);">return</span> E(enum_cast(lhs) | enum_cast(rhs));
}

<span class="hljs-keyword" style="color: rgb(252, 194, 140);">template</span>&lt;<span class="hljs-keyword" style="color: rgb(252, 194, 140);">typename</span> E&gt;
<span class="hljs-keyword" style="color: rgb(252, 194, 140);">constexpr</span> <span class="hljs-built_in" style="color: rgb(255, 255, 170);">std</span>::<span class="hljs-keyword" style="color: rgb(252, 194, 140);">enable_if_t</span>&lt;<span class="hljs-built_in" style="color: rgb(255, 255, 170);">std</span>::is_enum&lt;E&gt;::value, E&amp;&gt; <span class="hljs-keyword" style="color: rgb(252, 194, 140);">operator</span>|=(E&amp; lhs, E rhs) <span class="hljs-keyword" style="color: rgb(252, 194, 140);">noexcept</span>
{
    lhs = E(enum_cast(lhs) | enum_cast(rhs));
    <span class="hljs-keyword" style="color: rgb(252, 194, 140);">return</span> lhs;
}

<span class="hljs-keyword" style="color: rgb(252, 194, 140);">template</span>&lt;<span class="hljs-keyword" style="color: rgb(252, 194, 140);">typename</span> E&gt;
<span class="hljs-keyword" style="color: rgb(252, 194, 140);">constexpr</span> <span class="hljs-built_in" style="color: rgb(255, 255, 170);">std</span>::<span class="hljs-keyword" style="color: rgb(252, 194, 140);">enable_if_t</span>&lt;<span class="hljs-built_in" style="color: rgb(255, 255, 170);">std</span>::is_enum&lt;E&gt;::value, E&gt; <span class="hljs-keyword" style="color: rgb(252, 194, 140);">operator</span>&amp;(E lhs, E rhs) <span class="hljs-keyword" style="color: rgb(252, 194, 140);">noexcept</span>
{
    <span class="hljs-keyword" style="color: rgb(252, 194, 140);">return</span> E(enum_cast(lhs) &amp; enum_cast(rhs));
}

<span class="hljs-keyword" style="color: rgb(252, 194, 140);">template</span>&lt;<span class="hljs-keyword" style="color: rgb(252, 194, 140);">typename</span> E&gt;
<span class="hljs-keyword" style="color: rgb(252, 194, 140);">constexpr</span> <span class="hljs-built_in" style="color: rgb(255, 255, 170);">std</span>::<span class="hljs-keyword" style="color: rgb(252, 194, 140);">enable_if_t</span>&lt;<span class="hljs-built_in" style="color: rgb(255, 255, 170);">std</span>::is_enum&lt;E&gt;::value, E&amp;&gt; <span class="hljs-keyword" style="color: rgb(252, 194, 140);">operator</span>&amp;=(E&amp; lhs, E rhs) <span class="hljs-keyword" style="color: rgb(252, 194, 140);">noexcept</span>
{
    lhs = E(enum_cast(lhs) &amp; enum_cast(rhs));
    <span class="hljs-keyword" style="color: rgb(252, 194, 140);">return</span> lhs;
}

<span class="hljs-keyword" style="color: rgb(252, 194, 140);">template</span>&lt;<span class="hljs-keyword" style="color: rgb(252, 194, 140);">typename</span> E&gt;
<span class="hljs-keyword" style="color: rgb(252, 194, 140);">constexpr</span> <span class="hljs-built_in" style="color: rgb(255, 255, 170);">std</span>::<span class="hljs-keyword" style="color: rgb(252, 194, 140);">enable_if_t</span>&lt;<span class="hljs-built_in" style="color: rgb(255, 255, 170);">std</span>::is_enum&lt;E&gt;::value, E&gt; <span class="hljs-keyword" style="color: rgb(252, 194, 140);">operator</span>^(E lhs, E rhs) <span class="hljs-keyword" style="color: rgb(252, 194, 140);">noexcept</span>
{
    <span class="hljs-keyword" style="color: rgb(252, 194, 140);">return</span> E(enum_cast(lhs) ^ enum_cast(rhs));
}

<span class="hljs-keyword" style="color: rgb(252, 194, 140);">template</span>&lt;<span class="hljs-keyword" style="color: rgb(252, 194, 140);">typename</span> E&gt;
<span class="hljs-keyword" style="color: rgb(252, 194, 140);">constexpr</span> <span class="hljs-built_in" style="color: rgb(255, 255, 170);">std</span>::<span class="hljs-keyword" style="color: rgb(252, 194, 140);">enable_if_t</span>&lt;<span class="hljs-built_in" style="color: rgb(255, 255, 170);">std</span>::is_enum&lt;E&gt;::value, E&amp;&gt; <span class="hljs-keyword" style="color: rgb(252, 194, 140);">operator</span>^=(E&amp; lhs, E rhs) <span class="hljs-keyword" style="color: rgb(252, 194, 140);">noexcept</span>
{
    lhs = E(enum_cast(lhs) ^ enum_cast(rhs));
    <span class="hljs-keyword" style="color: rgb(252, 194, 140);">return</span> lhs;
}

<span class="hljs-keyword" style="color: rgb(252, 194, 140);">template</span>&lt;<span class="hljs-keyword" style="color: rgb(252, 194, 140);">typename</span> E&gt;
<span class="hljs-keyword" style="color: rgb(252, 194, 140);">constexpr</span> <span class="hljs-built_in" style="color: rgb(255, 255, 170);">std</span>::<span class="hljs-keyword" style="color: rgb(252, 194, 140);">enable_if_t</span>&lt;<span class="hljs-built_in" style="color: rgb(255, 255, 170);">std</span>::is_enum&lt;E&gt;::value, E&gt; <span class="hljs-keyword" style="color: rgb(252, 194, 140);">operator</span>~(E op) <span class="hljs-keyword" style="color: rgb(252, 194, 140);">noexcept</span>
{
    <span class="hljs-keyword" style="color: rgb(252, 194, 140);">return</span> E(~enum_cast(op));
}

}   <span class="hljs-comment" style="color: rgb(136, 136, 136);">// namespace enum_ops</span></pre>

测试代码：

<pre class="hljs" style="display: block; overflow-x: auto; padding: 0.5em; background: rgb(51, 51, 51); color: rgb(255, 255, 255);"><span class="hljs-keyword" style="color: rgb(252, 194, 140);">enum</span> <span class="hljs-keyword" style="color: rgb(252, 194, 140);">class</span> ChangeLevel : <span class="hljs-keyword" style="color: rgb(252, 194, 140);">unsigned</span> <span class="hljs-keyword" style="color: rgb(252, 194, 140);">int</span> {
    None = <span class="hljs-number" style="color: rgb(211, 99, 99);">0</span>,
    Local = <span class="hljs-number" style="color: rgb(211, 99, 99);">1</span> &lt;&lt; <span class="hljs-number" style="color: rgb(211, 99, 99);">0</span>,
    Server = <span class="hljs-number" style="color: rgb(211, 99, 99);">1</span> &lt;&lt; <span class="hljs-number" style="color: rgb(211, 99, 99);">1</span>,
    All = Local | Server
};

TEST(EnumOps, General)
{
    <span class="hljs-keyword" style="color: rgb(252, 194, 140);">using</span> <span class="hljs-keyword" style="color: rgb(252, 194, 140);">namespace</span> enum_ops;

    ChangeLevel level = ChangeLevel::None;
    level |= ChangeLevel::Local;
    level |= ChangeLevel::Server;

    EXPECT_TRUE(level == ChangeLevel::All);
    EXPECT_TRUE(enum_cast(level &amp; ChangeLevel::Local) != <span class="hljs-number" style="color: rgb(211, 99, 99);">0</span>);
    EXPECT_TRUE(enum_cast(level &amp; ChangeLevel::Server) != <span class="hljs-number" style="color: rgb(211, 99, 99);">0</span>);
}

TEST(EnumOps, NonEnumType)
{
    <span class="hljs-keyword" style="color: rgb(252, 194, 140);">using</span> <span class="hljs-keyword" style="color: rgb(252, 194, 140);">namespace</span> enum_ops;

    <span class="hljs-keyword" style="color: rgb(252, 194, 140);">unsigned</span> <span class="hljs-keyword" style="color: rgb(252, 194, 140);">int</span> l = <span class="hljs-number" style="color: rgb(211, 99, 99);">1</span>;
    <span class="hljs-keyword" style="color: rgb(252, 194, 140);">unsigned</span> <span class="hljs-keyword" style="color: rgb(252, 194, 140);">int</span> r = <span class="hljs-number" style="color: rgb(211, 99, 99);">1</span> &lt;&lt; <span class="hljs-number" style="color: rgb(211, 99, 99);">1</span>;
    <span class="hljs-keyword" style="color: rgb(252, 194, 140);">auto</span> rv = l | r;
    EXPECT_EQ(<span class="hljs-number" style="color: rgb(211, 99, 99);">3</span>, rv);

    <span class="hljs-comment" style="color: rgb(136, 136, 136);">// can't compile</span>
    <span class="hljs-comment" style="color: rgb(136, 136, 136);">//std::string s1 = "hello";</span>
    <span class="hljs-comment" style="color: rgb(136, 136, 136);">//std::string s2 = "world";</span>
    <span class="hljs-comment" style="color: rgb(136, 136, 136);">//s1 | s2;</span>
}</pre>
