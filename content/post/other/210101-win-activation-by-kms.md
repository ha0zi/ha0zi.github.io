---
title: '部分 Windows 利用 KMS 激活'
date: 2021-01-01T18:53:54+08:00
draft: false
categories: ["运维"]
tags: ["杂记"]

---
### 步骤

- 修改激活服务器地址
- 添加/修改激活密钥
- 执行激活操作

PS: 该教程需要使用的 *Windows* 系统需要是 *[VL][]* 版本的
<!-- more -->

[KMS][]
该教程来源于 Window7 贴吧, 目前已经删除, 以前逛的贴吧还是获取过很多的资源的.

### 修改激活服务器
首先如果是 *Windows10* 系统的话其实可以考虑去淘宝找一下, 可以购买永久激活版本的,也就是花费 *15* 元左右并不会很贵, 这种的激活码会把你的电脑硬件数据提交到中国的微软服务器, 如果意外重装了系统并不需要再次激活, 之所以提这个是因为 *KMS* 系统本身或许是没有问题的, 但并不代表提供这个服务的系统所有者的系统是"干净"的. 以下我所举例的 *KMS* 系统仅是我所知的, 并不保证它是"干净"的.

点击 *Windows* 系统的开始菜单输入 `cmd` 右键单击选择 *管理员模式运行*, 在黑窗口中输入 `slmgr /skms kms.03k.org`


### 输入对应的激活码
请将下方的 `[code]` 替换成对应系统的激活码
`slmgr /ipk [code]`

- *Windows Server* 系列:
```
Windows Server 2019 Datacenter：WMDGN-G9PQG-XVVXX-R3X43-63DFG
Windows Server 2019 Standard：N69G4-B89J2-4G8F4-WWYCC-J464C
Windows Server 2016 Datacenter：CB7KF-BWN84-R7R2Y-793K2-8XDDG
Windows Server 2016 Standard：WC2BQ-8NRM3-FDDYY-2BFGV-KHKQY
Windows Server 2012 R2 Server Standard：D2N9P-3P6X9-2R39C-7RTCD-MDVJX
WindowsServer 2012 R2 Datacenter：W3GGN-FT8W3-Y4M27-J84CP-Q3VJ9
Windows Server 2012 Server Standard：XC9B7-NBPP2-83J2H-RHMBY-92BT4
Windows Server 2012 Datacenter：48HP8-DN98B-MYWDG-T2DCC-8W83P-
Windows Server 2008 R2 Standard：YC6KT-GKW9T-YTKYR-T4X34-R7VHC
Windows Server 2008 R2 企业版：489J6-VHDMP-X63PK-3K798-CPX3Y
Windows Server 2008 R2 Datacenter：74YFP-3QFB3-KQT8W-PMXWJ-7M648
Windows Server 2008 Standard：TM24T-X9RMF-VWXK6-X8JC9-BFGM2
不带 Hyper-V 的 Windows Server 2008 Standard：W7VD6-7JFBR-RX26B-YKQ3Y-6FFFJ
Windows Server 2008 企业版：YQGMW-MPWTJ-34KDK-48M3W-X4Q6V
不带 Hyper-V 的 Windows Server 2008 企业版：39BXF-X8Q23-P2WWT-38T2F-G3FPG
Windows Server 2008 Datacenter：7M67G-PC374-GR742-YH8V4-TCBY3
不带 Hyper-V 的 Windows Server 2008 Datacenter：22XQ2-VRXRG-P8D42-K34TD-G3QQC
```
- 个人 *Windows* 系列:
```
Windows 8.1 专业版：GCRJD-8NW9H-F2CDX-CCM8D-9D6T9
Windows 8.1 企业版：MHF9N-XY6XB-WVXMC-BTDCT-MKKG7
Windows 7 专业版：FJ82H-XT6CR-J8D7P-XQJJ2-GPDD4
Windows 7 企业版：33PXH-7Y6KF-2VJC9-XBBR8-HVTHH
*Windows XP，Windows Server 2003不支持KMS。Windows Vista，Windows 8等不常用的系统不会列出（如有需要，请在评论区找我）。家庭版，Windows 7旗舰版，Windows 8.1（无标识任何版本）不支持KMS激活
错误代码原因请自行度娘
我更新一下，cmd（命令提示符）要以管理员身份运行，WindowsKMS密钥：
Windows 10 专业版：W269N-WFGWX-YVC9B-4J6C9-T83GX
Windows 10 专业工作站：NRG8B-VKK3Q-CXVCJ-9G2XF-6Q84J
Windows 10 企业版：NPPR9-FWDCX-D2C8J-H872K-2YT43
Windows 10 教育版：NW6C2-QMPVW-D7KKK-3GKT6-VCFB2
```


### 执行激活
`slmgr /ato`

目前写该文的时候 *kms.03k.org* 是在运行的, 如果不确定服务器是否可用的话, 可以访问 *03k.org* 进行查看一下.

水文完毕.


[VL]: https://zhidao.baidu.com/question/1951162734047407788.html "vol 是 volume licensing for organizations 的简称，中文即'团体批量许可证'。根据这个许可，当企业或者政内府需要大量购买容一软件时可以获得优惠。这种产品的光盘的卷标都带有 vol 字样，就取 volume 前3个字母，以表明是批量。"

[KMS]: https://baike.baidu.com/item/KMS/66195 "Key Management Service（简称:KMS），这个功能是在Windows Vista之后的产品中的一种新型产品激活机制，目的是Microsoft更好的遏制非法软件授权行为(盗版)。"
