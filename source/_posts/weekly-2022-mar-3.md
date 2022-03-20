---
title: 一周杂记 in Week 3 Mar 2022
categories: CODE-LIFE
date: 2022-03-20 20:57:09
tags:
---

感觉这周时间过得特别快。

## Life

- 周六去医院打了最后一针狂犬疫苗，只要疫苗是真的有效，那么至少 1-2 年内可以放肆撸猫（这个时间范围听医生说的）。
  有意思的是，这一次75块钱的针剂费依然走了医保，所以只能理解为大部分轮转医生搞不清楚疫苗不进入医保咯？
- 周中看了电影 _Wrath of Man_ 依然有盖里奇经典的非线性叙事，但是这一部感觉是盖里奇和杰森斯坦森的玩票作品，整个剧本都有点糟糕
- 周六看了本尼的 _The Courier_ 感觉也很一般，完全是浪费了 BC 的演技
- 周六打完疫苗回家的时候，楼上租客的黑猫（刀老师说是孟买猫）趁着我开门间隙跑到了我家里，当时给我整懵圈了。
  所幸最后发现是楼上 1702 两个女生租客的猫，其他合租的人没关门导致猫跑出去了。不过这猫期间在我家绕了半天最后在客厅拉了一坨屎...原来前面这么急不可耐是因为快要憋不住了 🤦‍♂️
  最后猫主人也帮我把屎清理干净了，我还用拖地机把涉事地砖都拖了一遍...
- 这周墙内各地的疫情局势变得愈发严重，上海的前同事们都开始居家办公了，而且也已经确定了下周要继续居家办公。
  不禁让人好奇，这清零政策能持续到几时？
- 周末打算约两个发小&小学同学下周末去看 _新蝙蝠侠_ 时得知一个刚结婚的同学老婆肚子已经不小了...把我震惊得...
  看来还是有一部人着急生孩子呀。

## Work

- 这周工作上的核心就是把合肥那边的 async-search 在 dev 跑通了，并且我们自己的 async-search consumer 也基本结束了实现，准备进入下一个阶段
- 在给 async-search consumer 打 rpm 包时发现 rpm 安装后程序一起来就抛异常，而且异常内容还很诡异。经过一番调查后发现是 jar 打包的问题。
  将程序 jar 包打成 rpm 时需要注意避免对 jar repack，因为 jar 本质上也是一个 zip 参考 https://stackoverflow.com/questions/32171605/rpm-extract-jar-files
- 同时还学习了一把 ansible vault encryption https://docs.ansible.com/ansible/latest/user_guide/vault.html
- 这周另外一个大变化则是开始恢复了看（技术）书，节奏感也慢慢在建立。
- 周末正式开始写 lumper，结果就遇到了一个下马威：commandline parse 需要支持 sub command，而我喜欢用的库 [argparse](https://docs.ansible.com/ansible/latest/user_guide/vault.html) 目前并不支持
  折腾了半天想了一个 workaround:
  1. root argparse 只有一个 argument，即一定要有的 command name，例如 `run` 或者 `build`
  2. 这个 argument 开启 `remaining()` 将后面所有参数收集起来，存到一个 `std::vector<std::string>` 里
  3. 为每个 command 创建一个 argparse 对象，将前面的参数作为 `parse()` 传入

  即本质上是人肉做了一次展开
  其实中间发现了另外一个原生支持 subcommands 的库 CLI11 但是看了一下接口设计感觉我不是很喜欢这种风格，而且就算原生支持了 subcommands，最后要做到符合预期的行为也要一番 tuning。
  所以最后索性直接用 argparse 开搞。

---

这周就这样，下周见
