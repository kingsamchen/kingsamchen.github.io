---
title: 基于 Docker 快速搭建用于学习 C/C++ 项目源码的环境
categories: ROGRAMMING
date: 2022-10-09 22:06:52
tags: [cpp, docker]
---
如果要研究学习的项目是 Java 或者 Golang 这种，那么其实没有必要专门基于 docker 容器搞出一个环境，直接在自己机器跑就行了。

但是对于很多上了规模的 C/C++ 项目来说（这里两个语言有共同之处所以放一起），在宿主机直接开搞经常会遇到这么几个问题：

- 依赖库如果能直接通过系统包管理，例如 apt，安装那么这些依赖库会直接装到系统目录下；如果碰到几个项目对同一个开源库的版本要求还不一样那很容易打架
- 如果依赖没法通过系统包管理，最常见的做法就是手动编译安装这些依赖库；虽然此时通常可以微操，例如设置 `PREFIX` 但是项目多了一样很容易玩脱手
- 虽然有些开源项目支持现场拉依赖现场编译，但是极大拉长了构建时间；毕竟我们的目的是研究项目源代码，二次开发另说
- 某些项目会提供 python 写的构建包装脚本，这些脚本通常是根据 `lsb_release` 来查询当前的发行版，使用对应的构建策略和流程；但是在 Linux Mint 这种表面上是独立发行版，但是骨子里其实是 ubuntu 的系统上构建，这些脚本就直接歇菜了。最典型的例子是 facebook/folly。虽然可以通过查阅构建脚本来微操，但是又臭又长的构建脚本看起来也很费时；而且难道每个这样的项目都要研究一遍人家的构建流程吗？

综上，如果能基于 docker container 快速搞出一个专门针对某个项目的环境，既可以做到环境隔离，不互相污染，又可以吃到主流支持的红利，何乐不为。

所以十一期间研究了一番，整了一个 Dockerfile

```dockerfile
FROM ubuntu:22.04

LABEL maintainer="kingsamchen@gmail.com"
LABEL release-date="2022-10-08"

ENV USER=dev
ENV PASSWD=dev

COPY ./sources.list /etc/apt/sources.list

RUN apt-get update && apt-get install -y \
        build-essential \
        cmake clang clangd \
        git gdb \
        net-tools ninja-build \
        python3 python3-pip \
        sudo \
        vim \
    && apt-get clean

RUN update-alternatives --install /usr/bin/cc cc /usr/bin/clang 40 && \
    update-alternatives --install /usr/bin/c++ c++ /usr/bin/clang++ 40

RUN useradd -s /bin/bash -m ${USER} && yes ${PASSWD} | passwd ${USER} && \
    echo ${USER}' ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers && \
    chmod 644 /etc/sudoers

USER ${USER}
```

基础系统镜像选择的是 ubuntu 22.04 LTS，和我的 Mint 一致；因为天朝环境实在过于恶劣，所以 apt 源用的清华的 TUNA，这里是单独一个[文件维护](https://github.com/kingsamchen/Eureka/blob/master/cxx-dev-dockerfile/sources.list)

安装了 C/C++ 开发的必备组件，并且默认编译器选择了 clang；额外安装 clangd 是因为 vscode remote 查看代码的时候需要 clangd 做 intellisense

考虑到除非项目自身卡死了 Makefile，基本都可以使用 ninja 构建来加速并行构建，所以安装了 ninja。

额外增加一个非 root 用户是使用习惯。

构建镜像

```shell
docker build -t "cxx-dev" .
```

假设我要创建一个容器专门研究 google/abseil-cpp，那么可以

```shell
cd /path/to/abseil-cpp
docker run -it --name study-absl --mount type=bind,source=$PWD,target=/home/dev/abseil-cpp cxx-dev
```

利用 volume 将宿主机的源码目录挂接到 docker 里。

容器起来之后就可以先把 abseil 需要的 libgtest-dev 和 google-mock 给装上，然后 vscode 直接 attach 到容器中就可以开始研究代码了。
