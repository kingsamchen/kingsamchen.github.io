---
title: Monthly Read Posts in Nov 2017
categories: PROGRAMMING
date: 2017-12-02 23:59:08
tags: [unicode, https, c++, lambda]
---
[The Absolute Minimum Every Software Developer Absolutely, Positively Must Know About Unicode and Character Sets (No Excuses!)](https://www.joelonsoftware.com/2003/10/08/the-absolute-minimum-every-software-developer-absolutely-positively-must-know-about-unicode-and-character-sets-no-excuses/)
[CppCon 2014: Unicode in CPP](https://channel9.msdn.com/Events/CPP/C-PP-Con-2014/Unicode-in-CPP)

编码这块的内容基本这两个 post 就足够了

---

[UNDERSTANDING SSL/TLS: PART 1 - WHAT IS IT?](https://blog.eleven-labs.com/en/understanding-ssltls-part-1/)

预警：作者只写了 part 1 之后就太监了...

---

[CppCon 2015: Implementation of a component-based entity system in modern C++](https://channel9.msdn.com/Events/CPP/CppCon-2015/CPPConD01V001)

不明觉厉的东西，没看太明白，似乎游戏开发用的多？

---

[CppCon 2015: Lambdas from First Principles](https://www.youtube.com/watch?v=WXeu4fj3zOs)

几乎是最好的 lambda 深入浅出型 talk

---

[CppCon 2015: Racing the Filesystem](https://www.youtube.com/watch?v=uhRWMGBjlO8)

各家系统文件系统操作的各种坑，顺带推销做着自己的 Boost.AFIO 库

然而我想了一下，好似平时都没怎么考虑过这些 racing conditions...

---

CppCon 2015: Effective C++ Implementation of Class Properties

最后给的实现方式很巧妙。但是实话说，工程上使用这些东西是个风险，如果语言自身不提供相应语法糖，我宁愿自己手写 getter/setter