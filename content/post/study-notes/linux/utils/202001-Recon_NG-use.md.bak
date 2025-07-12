---
title: 安全_信息收集_'Recon-NG'的使用
date: '2020-01-30 19:19:40'
updated: '2020-01-31 17:04:41'
toc: true
categories: 学习笔记
tags: [待分类]
---


## [recon-ng][]
关于 recon-ng 的教程在网络上都是 v4 的版本,它们都是过时的! 为此浪费了很多时间.依据 <黑客秘籍--渗透测试实用指南(第2版)> 教程过程的命令进行转换.另外特别说明 v5 版本中的模板需要使用 `marketplace`命令下载.本博文仅将书中的命令转换为 v5.1.1 版本命令.

```shell
cd /opt/recon-ng
./recon-ng

workspaces create SUCK
db insert domains

# 使用 Bing 查询域名
modules load recon/domains-hosts/bing_domain_web
run
# ..... 如上

#生成 html 报告页面
modules load reporting/html
options set CREATOR HP2
options set CUSTOMER HP2
run

```

[recon-ng]: https://github.com/lanmaster53/recon-ng