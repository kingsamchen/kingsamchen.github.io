---
title: Redigo 源码学习
categories: PROGRAMMING
date: 2020-08-21 00:11:04
tags: [redigo, golang]
---
Redigo 是目前比较流行的一个 Redis Client。

虽然我个人不太喜欢它的 API 设计，奈何这个库简单易用，连公司的 redis 基础库都是在 Redigo 上做的的一个魔改。

注：原生的 Redigo 并不支持 Redis Cluster，某B站的做法是通过 side-car 的方式启动一个 redis proxy 伴生容器，屏蔽 Redis Cluster 的通信细节。

既然用都用了，不如抽个时间研究一下这个库，看看其中是否有些设计值得借鉴。想想如此流行的一个基础组件库，如果代码质量过不去的话大概 issue 已经被愤怒的用户挤爆了...

因为我比较懒，所以只会挑一些个人感兴趣的部分研究。

本篇为总起大纲，具体分析学习的内容请自行跳转至指定页面。

- {% post_link src-study-redigo-redis-pool 连接池的设计 %}
- {% post_link src-study-redigo-wait-on-avail-conn 阻塞等待连接可用 %}
