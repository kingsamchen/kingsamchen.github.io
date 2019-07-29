---
title: 学习 MySQL 的一些吐槽
categories: CODE-LIFE
date: 2019-07-29 19:56:44
tags: [rant, mysql, 吐槽]
---
最近学习 MySQL 的过程中发现一些很坑的点，总结记录如下：

1. RDBMS 虽然理论很早就有了，但是近些年的实际应用进化已经导致各家独立。不同引擎实现不同，甚至连 spec 都不同。
2. 不考虑 Oracl, SQL Server 这种传统行业使用的，PostgreSQL 和 MySQL 之间差异性也不小，体现在 transaction, locking 上。甚至 MariaDB 和 MySQL 未来的差异可能也会变大。
3. 靠谱的资料很难找，尤其是针对研发而非 DBA 的靠谱的资料。
4. 国内互联网上至少 90% 的内容对于 MySQL 事务/锁的高级议题是有问题的。不知道是不是中文圈的尿性就是东拼西凑一大抄...其中涉及最多的有 1) 各种锁机制 2）MVCC 3）non-repeatable read 4）phantom read
5. 第四条是我在交叉对比各方资料后得出的结论，因为互相矛盾的点太多了
6. 对于 MySQL，最靠谱的还是参照官方 manual；比如官方的 glossary 就是一个很赞的东西，有了定义才能明确问题 https://dev.mysql.com/doc/refman/8.0/en/glossary.html
7. 自己本地动手实践也是一个很重要的途径

---

说起来 DB （之前 && 目前）应该是我最鶸的一块了。

在我还在上中学的时候（2005 ~ 2010）彼时的想法大概是未来找一份软件开发工程师的工作。

那会儿互联网产业还未兴起，软件开发差不多等同于写桌面客户端。我对数据库的印象停留在大企业内部自用的 Oracle 或者 Microsoft SQL Server，或者外包专用的 MySQL（LAMP），以及几乎没有人用的 Access / Visual Fox Pro；总之...都不是很好的印象。

于是本科期间几乎没怎么正经学过 RDBMS，唯一有印象的大概就是会写几条 SQL...

14 年毕业到转到后端前，有机会接触到的 DB 也只有 SQLite，单机存储数据库嘛，会随便写几条 SQL 就已经足够完成任务了，真是挺寒碜。