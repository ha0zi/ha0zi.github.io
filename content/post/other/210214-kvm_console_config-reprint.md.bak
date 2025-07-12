+++
title = 'KVM 使用 virsh 的 console 连接配置'
date = 2021-02-14T19:35:20+08:00
draft = false
categories = ["运维"]
tags = ["杂记","KVM"]
+++

[原文地址](https://blog.csdn.net/superbfly/category_1686089.html)
现象是连接时卡在这里就不动了

virsh console centos7.0
Connected to domain centos7.0
Escape character is ^]
<!-- more -->

解决方法（在虚拟机中做如下操作）
1. 修改  /etc/securetty 到最后添加 ttyS0
    `echo ttyS0 >> /etc/securetty`
2. 修改/etc/inittab 到最后添加  S0:12345:respawn:/sbin/agetty/ ttyS0 115200
    `echo 'S0:12345:respawn:/sbin/agetty/ ttyS0 115200' >> /etc/inittab`
3. 修改/etc/grub.conf 或者/etc/grub2.cfg
    在系统配置添加console=ttyS0
    我的是/etc/grub2.cfg，修改后的配置如下：
    ![image_1][]

之后重启虚拟机就可以




[image_1]: https://images.ha0zi.com/note/2024/04/27/image-20240427223543779.png
