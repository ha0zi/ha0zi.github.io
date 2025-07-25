---
title: 'Dockerfile 基础语法'
date: 2020-03-17T19:39:33+08:00
draft: false
categories: ["运维"]
tags: ["便捷笔记","Docker","待完善"]
---

## 简介

Dockerfile 是构建 Docker 镜像的文件,可以自定义更适合应用场景的环境.

## 指令

#### `FROM`
声明自定义镜像的基础镜像;后面的命令都是基于基础镜像环境使用的.

<!-- more -->
#### `RUN`
Docker build 运行时的运行指令;执行基于环境的命令.

#### `COPY`
复制指令;默认复制当前上下文的文件/目录.

#### `WORKDIR`
声明工作目录;声明镜像中的工作目录.

#### `EXPOSE`
声明容器需要使用的端口.

#### `CMD`
Docker run 运行时的运行指令,里面的参数为变量.

#### `ENTRYPOINT`
Docker run 运行时的运行指令,里面的参数为定量.



## 小技巧
- CMD 中执行的命令中包含了星号等通配符,可以通过 `/bin/sh` 来执行命令
  `CMD ["/bin/sh", "-c", "java", "-jar", "*.jar"]`
