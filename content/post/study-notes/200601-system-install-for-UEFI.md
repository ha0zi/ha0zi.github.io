---
title: 'UEFI引导-物理机安装CentOS8.1'
date: 2020-05-28T18:17:16+08:00
draft: false
categories: ["运维"]
tags: ["Linux","基础"]
---
### 步骤简介
- 下载镜像文件
- 下载 U盘 制作工具
- 制作系统镜像引导 U盘
- 使用引导盘安装系统

<!-- more -->
### 具体操作
##### 下载镜像文件(信息存在时效性, 该信息最后修改时间: 2020年05月28日18:12:40)
个人的建议是通过官网进行下载系统镜像,当然会遇到一些官网无法访问或者下载缓慢的情况.具体的下载步骤就不一一说明了,自行百度,一抓一大把.
- 无法访问官网,那么可以利用国内各大公司或者教育机构提供的镜像站进行下载;(以下列举几个,还有很多优秀的镜像网站未列出)
	- 公司开源镜像
		- https://mirrors.163.com/
		- https://mirrors.aliyun.com
		- https://mirrors.cloud.tencent.com/
	- 教育机构开源镜像
		- https://mirrors.tuna.tsinghua.edu.cn/
- 下载缓慢的情况,这种情况普遍常见,建议在官网中镜像列表中找到适合离自己最近的镜像站点.
	- [CentOS][]
	- Ubuntu(在官网搜索到以下两个地址,目的是安装系统,具体的区别就不细究,都可以下载镜像文件的目的)
		- https://launchpad.net/ubuntu/+cdmirrors
		- https://launchpad.net/ubuntu/+archivemirrors

##### 下载 U盘 制作工具
- Windows
	- [Rufus][]
	- [Win32 Disk Imager][]
	- [UltraISO(软碟通)][]
- Linux(暂无,目前使用 KVM 做 Win 镜像使用软件)
- [SOURCEFORGE][]
##### 制作镜像引导 U盘
###### 说明
- 操作的系统是 Windows 7 企业版,具体的软件使用就不累述了,太过简单化,如遇问题可以自行百度之后在互相沟通.
- 需要提醒的是,[Rufus][] 是需要联网的,如果你是在无网的环境下,请使用其他 镜像U盘制作工具.[UltraISO(软碟通)][]在使用过程中偶尔会出现一些'出乎意料'的错误.
- 本次使用的镜像为'CentOS-8.1.1911-x86_64-boot.iso'使用[Rufus][] 现在(2020年05月30日)是无法支持的,所以本次使用 [Win32 Disk Imager][] 进行镜像引导盘的制作.

  ![Rufus Error][]
- 使用 [Win32 Disk Imager][] 出现 U盘 无法也无法设置盘符等问题,这里可以参考 [Win32 Disk Imager U 盘恢复][] 的方法,进行操作可以完美解决你的问题.
###### U盘 无法识别
- 使用 `win` 键输入 `PowerShell` 在 `Windows PowerShell`右键管理员运行
- 输入`diskpart` 使用该软件
- 获取列表并选中 U盘,先清除它在进行格式化.具体参考下图.

![Diskpart Use][]

##### 使用引导盘安装系统
###### BIOS 引导
[BIOS][] 是主板上的程序,不同的主板开机进入它的按键也可能存在一定的差异,[进入 BIOS 参考][],这里就不多做说明了.本文的目的是 [UEFI][] 模式下安装系统,所以一下需要设置为该模式并设置 USB 的启动项优先级高于磁盘(需要注意的是不同的 [BIOS][] 程序的功能按键存在大同小异,如果这部分不适用你的机器,可使用翻译软件或者自行百度找到对应的设置).如果不清楚自己的 U盘叫什么,可以先不查进入该模式,在插入选择多出来的那一个.

![BIOS Select][]

###### 安装前注意
- 笔记本安装可能会遇到各种问题,例如"无法进入系统安装界面","安装完成后重启系统无法进入桌面\无法正常关机"等等.这些问题可能由各种问题组成,笔者遇到的经历有限仅简述自己遇到过的问题,并且提供我的解决方案
	- 先供上来自鸟哥的一些说明
		> 由於筆記型電腦加入了非常多的省電機制或者是其他硬體的管理機制，包括顯示卡常常是整合型的， 因此在筆記型電腦上面的硬體常常與一般桌上型電腦不怎麼相同。其實很簡單，只要在安裝的時候，告訴安裝程式的linux核心不要載入一些特殊功能即可。 最常使用的方法就是，在使用DVD開機時，選擇『』然後按下 [tab] 按鍵後，加入底下這些選項
		
		>`nofb apm=off acpi=off pci=noacpi`
	- 个人遇到过系统(Debian)无法进入安装界面以及无法启动桌面,自己多次查询发现是默认显卡驱动`nouveau`导致的,笔记本是双显卡,即在 进入系统/选择安装系统 之前按 `e` 键在 qui... 后面添加 `nouveau.modeset=0` 参数即可具体可参考[这里](https://www.ha0zi.com/articles/2019/12/24/1577119451755.html#toc_h5_5)的部分内容.

###### 目录设置推荐以及浅解
- 新手推荐家庭电脑目录分配
	
|目录 |容量 |
|:-- |:--|
| `swap` | 4G(为内存条的容量,如果你是 8G 内存条,及设置为8G) |
| `/boot` | 1G  |
| `/` | 30G(根据自己的硬盘,酌情添加不超过100G,会很浪费)  |
| `/var` | 5g |
| `/home` | 70G(剩余容量全部添加到这里)  |

	*注: 如果你有使用 `docker` 等程序且消耗存储容量较大,可以分配目录的时候给予 `/var` 目录较多的容量,个人电脑常规使用的话个人认为不超过 200G 是完全够用的,一般几十G就够了.如果你的虚拟机等实在过多,可以放置在 `/home` 目录中或者根据情况增大 `/var` 目录*
- 其他目的的系统请参考鸟哥的推荐方案: http://linux.vbird.org/linux_basic/0130designlinux.php#beforeinstall_partition
###### 安装 CentOS 8.1
- 我的安装心得就是,不要怕搞坏系统,看过引导说明之后,配合 *图标/文本* 经过大脑思考之后乱点即可,它又不会 GG,就算 GG 重新安装过就是了
- 安装选择,无法进入安装界面可以参考之前说明的'安装前注意'
![System Install Select][]
- 关于系统环境语言的选择,个人还是更倾向推荐使用默认英文,无论是日常使用还是后续扩展都会更方便一些,这边的方便也包括尽量少的可能遇到各种 'BUG'
![System Install Language][]
- 键盘以及输入法,建议添加一个中文输入法和设置`ctrl + shift`
![System Install Keyboard][]
- 这里系统环境语言还是需要添加中文的,如果你不想访问中文网站等出现全屏乱码的话,我还是建议你这么做
![System Install Language II][]
- 设置网络,使用默认即可,下面没拍到的地方可以设置主机名
![System Install Network][]
- 添加网络镜像站点,进行安装,可参考上方下载镜像的镜像网址加 `/BaseOS/x86_64/os/`,如果镜像文件是`CentOS-8.1.1911-x86_64-dvd1.iso`无视即可
![System Install OS Network Install][]
- 目录设置,也可参考 https://linux.vbird.org/linux_basic/centos7/0157installcentos7.php#centos_5 其中 `BIOS BOOT` 如果没有也可以忽视,你可以把他看做 `/boot/efi` 的功能,当然他们还是有很大的区别的.这里就不细说了.
![System Install Disk Home][]
![System Install Disk I][]
![System Install Disk II][]
![System Install Disk III][]
![System Install Disk VI][]
- 系统应用安装,我的实验硬件受限,大家根据自己的需求安装带 GUI 字样的即可,建议参考鸟哥的选择. https://linux.vbird.org/linux_basic/centos7/0157installcentos7.php#centos_4
![System Install App Select][]
- 最后设置一下 `root` 密码,以及设置一个管理员账号,理论上推荐使用管理员账号进行一切操作即可,需要 root 权限的时候使用 *`sudo +` 命令* 即可.
![System Installed][]















[CentOS]: https://www.centos.org/download/mirrors/
[Rufus]: https://rufus.ie/
[Win32 Disk Imager]: https://sourceforge.net/projects/win32diskimager/ "并没有发现官网,以这个代替,不过下载速度感人"
[UltraISO(软碟通)]: https://cn.ultraiso.net/xiazai.html
[SOURCEFORGE]: https://sourceforge.net "开源工具下载,工具多而全,但下载速度感人"
[DiskGenius]: https://www.diskgenius.cn
[Win32 Disk Imager U 盘恢复]: https://blog.csdn.net/catoop/article/details/102835855
[BIOS]: https://baike.baidu.com/item/bios/91424?fr=aladdin
[进入 BIOS 参考]: https://jingyan.baidu.com/article/b24f6c82c11ad286bfe5da23.html
[UEFI]: https://baike.baidu.com/item/%E7%BB%9F%E4%B8%80%E5%8F%AF%E6%89%A9%E5%B1%95%E5%9B%BA%E4%BB%B6%E6%8E%A5%E5%8F%A3/22786233?fromtitle=UEFI&fromid=3556240&fr=aladdin


[Rufus Error]: https://blog.ha0zi.com/images/2024/04/27/image-20240427215035613.png
[Diskpart Use]: https://blog.ha0zi.com/images/2024/04/27/image-20240427215255136.png
[BIOS Select]: https://blog.ha0zi.com/images/2024/04/27/image-20240427215421587.png
[System Install Select]: https://blog.ha0zi.com/images/2024/04/27/image-20240427215634153.png
[System Install Language]: https://blog.ha0zi.com/images/2024/04/27/image-20240427215720325.png
[System Install Keyboard]: https://blog.ha0zi.com/images/2024/04/27/image-20240427215839071.png
[System Install Language II]: https://blog.ha0zi.com/images/2024/04/27/image-20240427215927404.png
[System Install Network]: https://blog.ha0zi.com/images/2024/04/27/image-20240427220004110.png
[System Install Disk Home]: https://blog.ha0zi.com/images/2024/04/27/image-20240427220052426.png
[System Install Disk I]: https://blog.ha0zi.com/images/2024/04/27/image-20240427220130946.png
[System Install Disk II]: https://blog.ha0zi.com/images/2024/04/27/image-20240427220209264.png
[System Install Disk III]: https://blog.ha0zi.com/images/2024/04/27/image-20240427220306831.png
[System Install Disk VI]: https://blog.ha0zi.com/images/2024/04/27/image-20240427220403860.png
[System Install OS Network Install]: https://blog.ha0zi.com/images/2024/04/27/image-20240427220439107.png
[System Install App Select]: https://blog.ha0zi.com/images/2024/04/27/image-20240427220512886.png
[System Installed]: https://blog.ha0zi.com/images/2024/04/27/image-20240427220538978.png
