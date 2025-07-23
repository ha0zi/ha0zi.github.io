---
title: '使用 [acme.sh][] 实现自动迭代部署 ssl 证书'
date: '2025-07-13T03:50:38+08:00'
draft: false
categories: ["运维"]
tags: ["运维","自动化"]
---

##### 必要背景说明

- 阿里云免费获取 ssl 证书是 20 个 3 个月的有效期，多个证书的创建以及获取的时间是不一致的，导致感觉很繁琐， [acme.sh][] 可以解决这个问题

- 可以选择证书的 CA 运营商， 默认是 [ZeroSSL][]， 我根据网上的脚本修改为  [Let's Encrypt][] ， 但后面我发现这个是存在限制的。它们的差异比较 [参考信息](https://zerossl.com/letsencrypt-alternative/); 关于使用 [ZeroSSL][] 的配置参考[这里](https://github.com/acmesh-official/acme.sh/wiki/ZeroSSL.com-CA)

  ![image-20250712195200093](https://blog.ha0zi.com/images/2025/07/12/image-20250712195200093.png)

- [Let's Encrypt][] 存在限制， 但实际个人使用基本不用关注它，可以简单的理解为 7 天 5 次证书。当然不止如此， 这里是具体的[限制说明](https://letsencrypt.org/zh-cn/docs/rate-limits/)

  ![image-20250712195654544](https://blog.ha0zi.com/images/2025/07/12/image-20250712195654544.png)

##### 具体实操

###### 安装 [acme.sh][]

项目本身存在中文基本使用[说明](https://github.com/acmesh-official/acme.sh/wiki/%E8%AF%B4%E6%98%8E), 按照说明文档中的存在以下方式:

```shell
curl https://get.acme.sh | sh -s email=my@example.com
```

或

```shell
wget -O -  https://get.acme.sh | sh -s email=my@example.com
```

然而事实上在国内很难访问， 并且在 `https://get.acme.sh` 中也存在 `https://github.com/acmesh-official/acme.sh/wiki/Install-in-China` 这个地址的引导到 `https://gitee.com/neilpang/acme.sh`, 那么就通过获取 *git* 库在进行安装的方式

```shell
git clone https://gitee.com/neilpang/acme.sh.git
cd acme.sh
./acme.sh --install -m my@example.com
```

![image-20250712201304755](https://blog.ha0zi.com/images/2025/07/12/image-20250712201304755.png)

关于 *alias* 我使用的是 *zsh*, 它在 *.zshrc* 中添加了 `. "/root/.acme.sh/acme.sh.env"`

###### 更换 CA 服务商(可选)

```shell
acme.sh --set-default-ca --server letsencrypt
```

关于这里， 文档说明了 `ZeroSSL` 一个可能出现的问题， 会一直出现 `Pending，The CA is processing your order，please just wait.`,  而 `Let's Encrypt` 会出现的概率要小很多。

![image-20250712202126206](https://blog.ha0zi.com/images/2025/07/12/image-20250712202126206.png)

参与文档[地址](https://github.com/acmesh-official/acme.sh/wiki/%E8%AF%B4%E6%98%8E#%E4%BF%AE%E6%94%B9%E9%BB%98%E8%AE%A4-ca) 以及文中说明高级用法[地址](https://github.com/acmesh-official/acme.sh/wiki/How-to-issue-a-cert) (英文)

##### 生成证书

我的需求仅是将阿里云的域名进行自动化获取， 没有兴趣对它进行深度研究，只关注它的自动验证并获取证书；也可以通过这个更详细的[笔记博客](https://www.cnblogs.com/gshelldon/p/17970318#autoid-1-1-0)进行查看手动方式

获取阿里云密钥并授权后

```shell
export Ali_Key="你的 key"
export Ali_Secret="你的 secret"
acme.sh --issue --dns dns_ali -d my@example.com
```

然后他就会去帮你在阿里云中的域名解析中临时创建 `_acme-challenge.my` 然后删除它，你会收到阿里云的站内信提醒你删除。



##### [安装(复制)证书](https://github.com/acmesh-official/acme.sh/wiki/%E8%AF%B4%E6%98%8E#3-%E5%A4%8D%E5%88%B6%E8%AF%81%E4%B9%A6)

![image-20250724013625208](https://blog.ha0zi.com/images/2025/07/24/image-20250724013625208.png)

这里的输出的文件名和路径并没有严格的要求， 按照你实际管理的路径和文件名即可。例如使用 Nginx， 配合它的配置进行设置路径和文件名等，我用的是 Nginx。

```shell
acme.sh --install-cert -d my.example.com \
--key-file       /path/to/keyfile/in/nginx/my.example.com.key  \
--fullchain-file /path/to/fullchain/nginx/my.example.com.crt \
--reloadcmd     "service nginx force-reload"
```

![image-20250712204306338](https://blog.ha0zi.com/images/2025/07/12/image-20250712204306338.png)



##### 后续的其他说明

一开始我以为它的证书数量也是 20次/年, 证书的有效时长是 3 个月， 2 个月就会更新， 就疯狂的寻找，让重新获取证书的时间为 87 天， 然后发现 `--days` 以及 `--valid-to` `--valid-from ` 等参数其中 `--days` 控制本地重新获取新证书的操作， `--valid-to` 和 `--valid-from`  是对证书时效的操作。但是 [Let's Encrypt][] [ZeroSSL][] 没有 20次/年 的限制， 那么就无所谓了。



[acme.sh]: https://github.com/acmesh-official/acme.sh "一个实现ACME客户端协议的纯Unix shell脚本"
[ZeroSSL]: https://app.zerossl.com
[Let's Encrypt]: https://letsencrypt.org/
