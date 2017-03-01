---
title: Monthly Read Posts in Feb 2017
categories: PROGRAMMING
date: 2017-03-02 00:41:31
tags: [monthly read posts, restful api, c++, bitfields]
---
[RESTful架构风格下的4大常见安全问题](http://insights.thoughtworkers.org/security-issues-in-restful/)

- Don't forget to check correlations between resources in request URL
- Some rare used HTTP headers might be helpful
- Restrict API request frequency

这篇略水，某些常见的问题反而都没有提到，例如大木老师遇到的[这个问题](http://www.daozhihun.com/p/2119)

---

[Safe Bitfields in C++](http://preshing.com/20150324/safe-bitfields-in-cpp/)

Main observation: Use union to implement/simulate bitfields.

Each bitfield is implemented by using a single union member with mask and bit-length being set up initially.

非常 stunning 的一个实现，但是很可惜，根据 comments 这个实现仍然是 undefined behavior，because *it call member functions on inactive union members*.