+++
title = 'apt 中断后重装软件'
date = 2020-12-23T13:56:41+08:00
draft = false
categories = ["运维"]
tags = ["Linux","运维"]
+++
### 环境

- Debian 10 [amd64]

### 具体操作

#### 查询具体的中断操作

中断了 `apt` 操作之后假设你并不清楚具体在哪里中断的,可以先在 */var/log/apt/term.log* 中进行查询之前具体的操作.例如:

<!-- more -->
```shell
Start-Date: 2020-12-23  13:42:08
Commandline: apt upgrade
Requested-By: hz (1000)
Upgrade: libwebkit2gtk-4.0-37:amd64 (2.30.3-1~deb10u1, 2.30.4-1~deb10u1), gir1.2-webkit2-4.0:amd64 (2.30.3-1~deb10u1, 2.30.4-1~deb10u1), libjavascriptcoregtk-4.0-18:amd64 (2.30.3-1~deb10u1, 2.30.4-1~deb10u1), gir1.2-javascriptcoregtk-4.0:amd64 (2.30.3-1~deb10u1, 2.30.4-1~deb10u1)
End-Date: 2020-12-23  13:42:16

```

这个就是我之前的操作,我们可以看到当时我正在进行系统更新操作,更新的软件分别是 `libwebkit2gtk-4.0-37`, `gir1.2-webkit2-4.0`, `libjavascriptcoregtk-4.0-18`, `gir1.2-javascriptcoregtk-4.0`.[查询资料信息来源]

#### 重装软件

我们已经知道了之前安装的操作步骤了,那么我们就可以重新安装之前的软件,避免因为中断导致的系统文件缺失错误.

`sudo apt-get --reinstall install libwebkit2gtk-4.0-37 gir1.2-webkit2-4.0 libjavascriptcoregtk-4.0-18 gir1.2-javascriptcoregtk-4.0`

正常的安装软件运行执行完成.



[查询资料信息来源]:  https://qastack.cn/unix/79050/can-i-rollback-an-apt-get-upgrade-if-something-goes-wrong
[重装资料信息来源]: https://blog.csdn.net/qintaiwu/article/details/73741976
