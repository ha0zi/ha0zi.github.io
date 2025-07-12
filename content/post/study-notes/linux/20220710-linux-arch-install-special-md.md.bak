+++
title = '记录一次不一样的 Arch Linux 安装'
date = 2022-07-10T11:02:41+08:00
draft = false
categories = ["Linux"]
tags = ["Linux","base","install"]
+++
### 前言
[Arch Linux] 的安装和其他傻瓜式的安装程序相比，显得那么与众不同，但更让人心动的是它的 [AUR] 仓库的丰富性。在此之前我曾安装并体验过一段时间，结果错误的更新配置导致系统的崩溃。这当然是我极不愿意忍受的，为此我准备使用 [Debian] 来作为我台式电脑的系统。但 [Debian] 的稳健让它的自带驱动不能很好的满足我的新设备，又意外的在 [清华源] 发现了 [Arch Linux GUI] 中我垂涎已久的 [i3wm]。
[Arch Linux GUI] 没有很好的处理 [LVM 分区] 的缘故，让我花费了大量的时间进行这次安装。我的处理方法是先使用本地正常分区安装，然后将已安装好的系统移到配置号的 [LVM 分区]。
我不认为傻瓜式的 *GUI* 安装需要再次说明，唯一值的一提的是我使用的是 `archlinux-gui-i3-2022.05-x86_64.iso`, 以往没有使用过 [i3wm] 导致我花费了很久的是指摸索，最后在 [Arch Linux GUI] 中找到相关说明这里备份一份
> Basic Keybindings to help you get started: ( mod = windows key)
>
> Open Terminal Window (Kitty): `mod + enter`
>
> Launch Program Launcher (rofi): `mod + d`
>
> Close Any Window: `mod + shift + x`
>
> Open/Edit Config file: `vim ~/.config/i3/config`
>
> Configure Polybar: `vim ~/.config/polybar/config`

我发现这里说的关闭所有窗口的快捷键在大部分情况下都不好用，远不如 `esc` 按键来的有用。
<!-- more -->
### 简单说明
安装完之后，对系统进行移动的思路是学习 [将已经存在的archlinux迁移到lvm]
在操作这之前，我需要先对磁盘进行好分区，这里通过步骤主要通过 `parted` 这个命令行工具进行分区 [GPT 分区]。[Linux磁盘的分区操作](https://blog.csdn.net/TE_M_D/article/details/120910408#t7)

在操作这些之前，我简单说明一下，在固有的印象中，系统需要进行以下分区

|盘符 |大小 |文档系统 | 说明|
|:-- |:-- |:-- |:-- |
|`BIOS System` |*100M* |*fat32* |就是 GPT 分割表可能會使用到的項目，若你使用 MBR 分割，那就不需要這個項目了！ |
|`EFI System`(`/boot/efi`) |*100M* |*fat32* |引导程序 [UFEI] |
|`/boot` |*1G* |*ext4* |這個目錄主要在放置開機會使用到的檔案，包括Linux核心檔案以及開機選單與開機所需設定檔等等。|
|`swap` |${实际内存/2} |*swap* |就是磁碟模擬成為記憶體，由於swap並不會使用到目錄樹的掛載，所以用swap就不需要指定掛載點喔。*忘了哪里看到的理论，这个实际操作 8G 就没有太大的意义*|
|`/` |*30G* |*ext4* |系统根 |
|`/home` |*50G* |*ext4* |用户目录 |
|`/var` |*100G* |*ext4* |临时文件、系统日志存放等，详细的去看[鸟哥的Linux私房菜]; 我这里使用 [Docker] 会占用大量的该目录|
|`/opt` |*30G* |*ext4* |第三方協力軟體 |

其中 `/boot`，`EFI System`, `BIOS System` 需要实际安装在标准分区中，其他分区可以安装在 [LVM 分区] 中。

### 实际操作
到这里需要你拥有一个**系统安装的启动盘**和一个**已经安装好的系统**，使用启动盘进入安装系统。

1. 查看磁盘，使用 `parted` 进行磁盘分区, (这里预设 `/dev/sda` 磁盘为 *512G*)
  - `sudo fdisk -l` (磁盘的辨别，可以通过名称进行 [判别](https://linux.vbird.org/linux_basic/centos7/0130designlinux.php#hardware_no) 受限博文书写的时间固态的名称一般为 `/dev/nvme*`)
  - `sudo parted /dev/sda` (参考 [将已经存在的archlinux迁移到lvm]))
    - `mklabel`
      - `GPT`
    - `mkpart`
      - `bios` (*Partition name? []?*)
      - `fat32` (*File system type? [ext2]?*)
      - `0G` (Start?)
      - `120M` (end?)
    - `mkpart` `efi` `fant32` `120M` `240M`
    - `mkpart` `boot` `ext4` `240M` `1260M`
    - `mkpart` `lvm` `ext4` `1260M` `512G`
    - `p` (查看分区)
    - `quit` (退出)
2. 配置 [LVM 分区]（这里使用 [vgcreate] 和 [lvcreate] 进行创建， 先创建逻辑卷组，在根据逻辑卷组创建逻辑分区, 被创建出来的磁盘分区可以在 `/dev/lvm-vg` 目录下 *lvm-vg* 是一个变量，根据 `vgcreate` 创建命名获取）
  - `vgcreate lvm-vg /dev/sda4` (这个 `/dev/sda4` 也是根据 `fdisk` 查询出来的，也就是在 `mkpart` 时创建 *name* 为 *lvm* 的分区)
  - `lvcreate -L 30G -n root lvm-vg` (`-L` 指定容量, `-n` 指定创建磁盘名称和 *LVM 卷组* 名称)
  - `lvcreate -L 8G -n swap lvm-vg`
  - `lvcreate -L 50G -n home lvm-vg`
  - `lvcreate -L 100G -n var lvm-vg`
  - `lvcreate -L 30G -n opt lvm-vg`
3. 格式化磁盘
  - `sudo mkfs.ext4 /dev/lvm-vg/root`
  - `sudo mkfs.ext4 /dev/lvm-vg/home`
  - `sudo mkfs.ext4 /dev/lvm-vg/var`
  - `sudo mkfs.ext4 /dev/lvm-vg/opt`
  - `sudo mkswap /dev/lvm-vg/swap`
4. 将之前安装的系统复制到新创建的 [LVM 分区] 的 *root* 分区中 (这里预设使用的安装好的系统磁盘为 `/dev/sdb`)
  - `sudo dd if=/dev/sdb of=/dev/lvm-vg/root` (root 为创建的 *LVM 分区*，需要等待一些时间)
5. 挂载系统并处理必要配置文件
  - 挂载复制好的系统
    - `sudo mount /dev/lvm-vg/root /mnt`
  - 挂载其他分区，复制对应的数据
    - `mkdir -P /data/boot /data/var /data/opt /data/home`
    - `sudo mount /dev/sda3 /data/boot` (对应 `mkpart boot` 那个分区)
    - `sudo mount /dev/lvm-vg/home /data/home`
    - `sudo mount /dev/lvm-vg/var /data/var`
    - `sudo mount /dev/lvm-vg/opt /data/opt` (可忽略)
    - `sudo cp -r /mnt/boot /data/` `rm -rf /mnt/boot/*`
    - `sudo cp -r /mnt/var /data/` `rm -rf /mnt/var/*`
    - `sudo cp -r /mnt/home /data/` `rm -rf /mnt/home/*`
  - 修改对应的配置
    - `vim /mnt/etc/mkinitcpio.conf`
      - 进行 [mkinitcpio.conf 修改] (这里的修改很可能不同，请看引用，可能是 `sd-lvm2`)
      - `HOOKS="base udev ... block lvm2 filesystems"`
    - `mkinitcpio -P`
    - 根据 `blkid /dev/sda3` 等对应的获取 *UUID* 配置到 `/mnt/etc/fstab` (配置内容参考 [fstab 文件详解])
    - 全部卸载 `sudo umount` `/mnt` `/data` 对应目录
    - 使用 `sudo resize2fs -f /dev/lvm-vg/root` 刷新文件系统的真实大小
    - 使用 `sudo mount` 挂载 `/mnt` 组成一个完整的系统 (别忘了 `/boot` 和 `/boot/efi` 的挂载)
    - 使用 `arch-chroot /mnt` 进入这个系统
    - 使用 `grub-mkconfig -o /boot/grub/grub.cfg` 重新生成引导文件
  - 退出，重启，拔出启动盘，搞定



[系统相关]: 系统相关
[arch Linux]: https://archlinux.org/
[Debian]: https://www.debian.org/


[资源相关]: 资源相关
[清华源]: https://mirrors.tuna.tsinghua.edu.cn/
[Arch Linux GUI]: https://archlinuxgui.in/
[i3wm]: https://i3wm.org/ "一种桌面，我的映像标签为*简洁*和*解放鼠标*"
[鸟哥的Linux私房菜]: https://linux.vbird.org/ "很棒，极力推崇"
[Docker]: https://www.docker.com/ "快速部署容器"

[纯说明相关]: 纯说明相关
[AUR]: https://zhuanlan.zhihu.com/p/129855163
[LVM 分区]: https://baike.baidu.com/item/LVM/6571177 "這是一種可以彈性增加/削減檔案系統容量的裝置設定"
[GPT 分区]: https://linux.vbird.org/linux_basic/centos7/0130designlinux.php#partition "相关知识可以看看鸟哥这本书，真的很棒"
[根目录的意义]: https://linux.vbird.org/linux_basic/centos7/0210filepermission.php#dir_fhs_root
[UFEI]: https://linux.vbird.org/linux_basic/centos7/0130designlinux.php#partition_bios_uefi
[将已经存在的archlinux迁移到lvm]: https://zhuanlan.zhihu.com/p/130445778
[mkinitcpio.conf 修改]: https://wiki.archlinux.org/title/LVM_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#%E9%85%8D%E7%BD%AEmkinitcpio
[fstab 文件详解]: https://blog.csdn.net/youmatterhsp/article/details/83933158

[终端工具命令]: 终端工具命令
[vgcreate]: https://linux265.com/course/linux-command-vgcreate.html
[lvcreate]: https://linux265.com/course/linux-command-lvcreate.html
