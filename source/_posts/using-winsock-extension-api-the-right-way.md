---
title: 使用 Winsock Extension API 的正确姿势
categories: PROGRAMMING
date: 2018-02-14 17:21:40
tags: [winsock, network programming, socket]
---
所谓的 Winsock Extension API 指的是微软专门额外添加，由应用层 mswsock.dll 导出的函数集，包括 `AcceptEx()`, `DisconnectEx()` .etc

使用这部分 API 的原因是它们通常支持更新更牛X的特性，且几乎大多数都是异步函数。

但是因为这部分是应用层函数，直接链接 mswsock.dll 会导致每个函数调用内部都会进行一次由系统完成的动态的函数地址确定，相当于 `GetProcAddr()`，由此引发性能问题。

由此衍生出，首先使用 `WSAIoctl()` 获取这部分 extension API 的函数地址，然后利用函数地址调用的做法。

注：性能开销这部分可参考 [这里](https://stackoverflow.com/questions/4470645/acceptex-without-wsaioctl)。回答引用的 MSDN Magazine 上的文章已经不可访问了，算是一个遗憾。

然而关于这块包括 MSDN 上都是一些不详尽的片段，或者根本没提到。

例如我在学习这部分东西时，一个最大的问题就是 `WSAIoctl()` 函数无论如何需要一个 socket handle，然而这个需要和我们的目的其实没有关系。

围绕这个 socket handle 就可以催生出各种问题，但是 MSDN 上甚至没有对这个做哪怕一些解释...

通过翻查汇总各种相关的代码片段，以及结合 MSDN 上描述的内容，个人总结出了一个推荐的做法：

1. 使用一个全局的管理者来保存你需要的扩展函数指针。这里全局的管理者可以是一个 singleton，或者用 namespace 包裹的一些全局对象
2. 在初始化时机创建一个 TCP socket，利用这个 socket 去调用 `WSAIoctl()`，拿到扩展函数地址
3. 经过上一部之后，外界使用者就可以和使用普通的 API 一样使用这几个扩展函数

至于是不是要回收刚才创建的 socket，这个看自己喜好...

参考部分代码：

<pre class="hljs" style="display: block; overflow-x: auto; padding: 0.5em; background: rgb(51, 51, 51); color: rgb(255, 255, 255);"><span class="hljs-keyword" style="color: rgb(252, 194, 140);">namespace</span> {

ScopedSocketHandle sock;

<span class="hljs-keyword" style="color: rgb(252, 194, 140);">template</span>&lt;<span class="hljs-keyword" style="color: rgb(252, 194, 140);">typename</span> P&gt;
<span class="hljs-function"><span class="hljs-keyword" style="color: rgb(252, 194, 140);">void</span> <span class="hljs-title" style="color: rgb(255, 255, 170);">GetExtensionFunctionPointer</span><span class="hljs-params">(P&amp; pfn, GUID guid)</span>
</span>{
    DWORD recv_bytes = <span class="hljs-number" style="color: rgb(211, 99, 99);">0</span>;
    <span class="hljs-keyword" style="color: rgb(252, 194, 140);">auto</span> rv = WSAIoctl(sock.get(), SIO_GET_EXTENSION_FUNCTION_POINTER, &amp;guid, <span class="hljs-keyword" style="color: rgb(252, 194, 140);">sizeof</span>(guid), &amp;pfn,
                       <span class="hljs-keyword" style="color: rgb(252, 194, 140);">sizeof</span>(pfn), &amp;recv_bytes, <span class="hljs-literal" style="color: rgb(252, 194, 140);">nullptr</span>, <span class="hljs-literal" style="color: rgb(252, 194, 140);">nullptr</span>);
    ENSURE(CHECK, rv == <span class="hljs-number" style="color: rgb(211, 99, 99);">0</span>)(rv)(WSAGetLastError()).Require();
}

}   <span class="hljs-comment" style="color: rgb(136, 136, 136);">// namespace</span>

<span class="hljs-keyword" style="color: rgb(252, 194, 140);">namespace</span> winsock_ctx {

LPFN_ACCEPTEX AcceptEx = <span class="hljs-literal" style="color: rgb(252, 194, 140);">nullptr</span>;
LPFN_DISCONNECTEX DisconnectEx = <span class="hljs-literal" style="color: rgb(252, 194, 140);">nullptr</span>;

<span class="hljs-function"><span class="hljs-keyword" style="color: rgb(252, 194, 140);">void</span> <span class="hljs-title" style="color: rgb(255, 255, 170);">Init</span><span class="hljs-params">()</span>
</span>{
    WSADATA data {<span class="hljs-number" style="color: rgb(211, 99, 99);">0</span>};
    <span class="hljs-keyword" style="color: rgb(252, 194, 140);">auto</span> result_code = WSAStartup(MAKEWORD(<span class="hljs-number" style="color: rgb(211, 99, 99);">2</span>, <span class="hljs-number" style="color: rgb(211, 99, 99);">2</span>), &amp;data);
    ENSURE(THROW, result_code == <span class="hljs-number" style="color: rgb(211, 99, 99);">0</span>)(result_code).Require();

    sock.reset(socket(AF_INET, SOCK_STREAM, <span class="hljs-number" style="color: rgb(211, 99, 99);">0</span>));
    ENSURE(CHECK, !!sock)(WSAGetLastError()).Require();

    GetExtensionFunctionPointer(AcceptEx, WSAID_ACCEPTEX);
    GetExtensionFunctionPointer(DisconnectEx, WSAID_DISCONNECTEX);

    sock.reset();

    <span class="hljs-built_in" style="color: rgb(255, 255, 170);">printf</span>(<span class="hljs-string" style="color: rgb(162, 252, 162);">"-*- Windows Socket Library Initialized -*-\n"</span>);
}

<span class="hljs-function"><span class="hljs-keyword" style="color: rgb(252, 194, 140);">void</span> <span class="hljs-title" style="color: rgb(255, 255, 170);">Cleanup</span><span class="hljs-params">()</span>
</span>{
    WSACleanup();

    <span class="hljs-built_in" style="color: rgb(255, 255, 170);">printf</span>(<span class="hljs-string" style="color: rgb(162, 252, 162);">"-*- Windows Socket Library Cleaned -*-\n"</span>);
}

}   <span class="hljs-comment" style="color: rgb(136, 136, 136);">// namespace winsock_ctx</span>
</pre>

完整的 sample 可以看[这里](https://github.com/kingsamchen/Eureka/blob/master/ConcurrentHttpServer/ConcurrentHttpServerWin/src/winsock_ctx.h)
