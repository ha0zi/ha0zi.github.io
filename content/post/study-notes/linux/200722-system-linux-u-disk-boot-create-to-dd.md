---
title: 'Linux 环境下使用 dd 创建 U盘 启动盘'
date: 2020-07-22T20:17:42+08:00
draft: false
categories: ["运维"]
tags: ["Linux","运维"]
---
### 步骤简介
- 卸载 U盘
- 格式化
- 执行启动盘制作并同步

<!-- more -->
### 具体实现
##### 卸载 U盘
鉴于目前我们常见的我所知的系统镜像文件大多在 10GiB 以内,所以这边建议大家准备一个 16GB 的移动硬盘来制作系统安装启动盘.

将移动硬盘插入电脑 USB 接口中,我们可以在文档管理器中看到一个移动硬盘的显示识别到一个移动硬盘,这个时候我们可以关闭文档管理器,打开我们的终端进行操作.
![show USB flash disk][]

我们可以使用`sudo fdisk -l`查看当前系统所识别到的[档案系统]并判断哪一个是你的 U盘.譬如我的电脑中显示的`/dev/sdc1`就是我的 U盘 判断依据的话,首先可以看哪一个档案系统的存储容量和你的移动硬盘最接近,其次如果还是无法判断,那么可以先拔掉移动硬盘查询一次,那么少的那个档案系统就是你的移动硬盘无疑了.(如果硬盘过多,可以使用`sudo lsblk`又或者`sudo blkid`进行档案系统查询)
![show fdisk -f][]

使用`sudo umount /dev/sdc*`(ps:umount 命令后接的路径以你实际的移动硬盘路径为准),执行卸载命令.这个时候有的朋友会遇到`umount: /dev/sdc1: not mounted.`这个提示时不用慌,它只是告诉你 `/dev/sdc1` 并没有挂载,所以你可以直接进行下一步格式话操作即可.

##### 格式化
现在我们已经卸载了我们的移动硬盘,这个时候我们再次输入`sudo umount /dev/sdc*`会提示我们并没有这个档案系统挂载,那么我们可以进行 `sudo mkfs.vfat -I /dev/sdc1`这个时候它会显示`mkfs.fat 4.1 (2017-01-24)`这个提示信息,我们只需要等待它格式化完成即可.

如果你在格式化的时候收到提示的是
```
mkfs.fat 4.1 (2017-01-24)
mkfs.vfat: /dev/sdc1 contains a mounted filesystem.
```
那么恭喜你,你并没有卸载 `/dev/sdc1` 这个档案系统,这里就建议你再确认一遍你所卸载的是否就是你准备作为启动盘的移动硬盘,并正确的使用上面提到的卸载 U盘 的内容进行卸载.

##### 执行启动盘制作并同步
这个需要准备一个系统镜像文件一般一`*.iso`结尾的文件,具体的获取方式可以参考[UEFI引导-物理机安装CentOS8.1 中的下载镜像文件说明].

在此这些操作的过程中我们都没有使用过任何其他的工具对移动硬盘进行过操作,(如果出现类似'不存在这个挂载'或则'该挂载被其他工具所占用',那么你就需要判断你所操作的档案系统是否是你的移动硬盘,以及判断是否存在什么应用一直在访问你的移动硬盘里面的文件.遇到此类情况可以联系我一起进行学习探讨 xthcs1994@163.com)那么使用`dd`命令进行系统安装启动盘制作.
`dd if=/home/${USER}/Downloads/*.iso of=/dev/sdc1 bs=4M && sudo sync`

这里需要注意的是`if=/home/${USER}/Downloads/*.iso`这里是一个[绝对路径],如果具体`if=`后面的内容依据你的镜像文件真实的物理路径填写(注意[权限问题]哦);上述命令中`bs=4M`这里面是指所制作的启动盘
Block大小,笔者这里也是一知半解,就不做过多的误导了,如果有兴趣可以看下[档案系统]的概念.

最后这里说明一下`sudo sync`的意思,这里涉及到了系统操作时对一个文件写入的概念,你可以理解为当你输入`dd if=* of=* bs=4M`的时候系统并没有马上写入到你的移动硬盘中,需要执行`sync`让系统立刻执行将内容写入.如果有兴趣了解可以[计算机概念].

经过一段时间的等待,重新出现光标,大功告成,我们重新拔插一下 U盘 发现了 U盘 名字被修改,里面也添加了很多其他的内容.使用完后,有朋友需要重新将这个 U盘 作文存储容器,重复 1\2 步操作即可.


[show USB flash disk]: https://blog.ha0zi.com/images/2024/04/27/image-20240427214711530.png
[show fdisk -f]: https://blog.ha0zi.com/images/2024/04/27/image-20240427214757487.png
[档案系统]: https://linux.vbird.org/linux_basic/centos7/0230filesystem.php
[权限问题]: https://linux.vbird.org/linux_basic/centos7/0210filepermission.php
[计算机概念]: https://linux.vbird.org/linux_basic/centos7/0105computers.php
[UEFI引导-物理机安装CentOS8.1 中的下载镜像文件说明]: /study-notes/200601-system-install-for-uefi/
[绝对路径]: https://baike.baidu.com/item/%E7%BB%9D%E5%AF%B9%E8%B7%AF%E5%BE%84/481185
