---
title: Acquire/Release Operation 和 Acquire/Release Fence
categories: PROGRAMMING
date: 2019-08-31 23:26:19
tags: [cpp, memory fence, memory ordering]
---

其实是 Jeff Preshing 的 [Acquire and Release Fences Don't Work the Way You'd Expect](https://preshing.com/20131125/acquire-and-release-fences-dont-work-the-way-youd-expect/) 这篇文章的笔记。

acquire-release operation 和 acquire-release fence 之间存在非常 subtle 的区别，很多人都在这个上犯过错误。

比如 Jeff Preshing 自己，以及 Herb Sutter。

个人习惯，笔记一开始就是在 onenote 里，懒得重新做一遍 markdown 了，索性转成 PDF 直接内嵌。

<iframe src="https://onedrive.live.com/embed?cid=4496A33F126535A6&resid=4496A33F126535A6%2149246&authkey=AG4NakEdjaO2xII&em=2" width="800" height="600" frameborder="0" scrolling="no"></iframe>
