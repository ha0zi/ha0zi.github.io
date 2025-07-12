+++
title = '重装 Debian9 系统之后的坑'
date = 2019-12-24T00:44:11+08:00
draft = false
categories = ["随笔"]
tags = ["便捷笔记"]
+++
### 必装
##### 添加 apt 源
修改 apt 源`vi /etc/apt/source.list`
注释第五行
`#deb cdrom:[Debian GNU/Linux 10.2.0 _Buster_ - Official amd64 DVD Binary-1 20191116-09:57]/ buster contrib main`
在末尾添加清华源
```
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ buster main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ buster main contrib non-free
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ buster-updates main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ buster-updates main contrib non-free
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ buster-backports main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ buster-backports main contrib non-free
deb https://mirrors.tuna.tsinghua.edu.cn/debian-security buster/updates main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian-security buster/updates main contrib non-free
```
`apt update`之后进行`apt install vim`

<!-- more -->
##### 添加用户到 sudo
sudo 管理文件是`/etc/sudoers`,并在第20行后添加
```shell
# User privilege specification
root	ALL=(ALL:ALL) ALL
username	ALL=(ALL:ALL) ALL
```


##### 输入法
```shell
sudo apt install fcitx libqt4-declarative
# 到官网下载搜狗输入法 linux 版本
dpkg -i sougou**.deb
sudo apt install -f
dpkg -i sougou**.deb
```


##### 设置触摸板
复制`/usr/share/X11/xorg.conf.d`到`/etc/X11`后修改`/usr/share/X11/xorg.conf.d`
```shell
sudo cp -R /usr/share/X11/xorg.conf.d /etc/X11/
sudo vim /usr/share/X11/xorg.conf.d/40-libinput.conf
```
在37行后添加设置
```shell
Section "InputClass"
        Identifier "libinput touchpad catchall"
        MatchIsTouchpad "on"
        MatchDevicePath "/dev/input/event*"
        Driver "libinput"
	# 启动轻触即点击
        Option "Tapping" "on"
        # 启动自然滚动
        Option "NaturalScrolling" "true"
        # 点击方式为手指,单指左键,双指右键,三指中键
        Option "ClickMethod" "clickfinger"
        # 键入是禁用触摸板
        Option "DisableWhileTying" "true"
EndSection
```


##### 双屏显示 [原始资料地址](https://forum.ubuntu.org.cn/viewtopic.php?p=3218361)
首先需要正常 启动/关闭 计算机,然后安装 NVIDIA 显卡驱动,再设置显卡配置,最后设置双屏.
- 因为是双显卡笔记本,因此总有各种问题.Debian 10 系统安装之后会出现各种无语的问题,譬如:账户登录的时候会黑屏,关机的时候会'卡死',简单的判断发现是因为 nouveau 开源显卡驱动导致的.解决的方法是在启动的时候在 qui... 后面添加`nouveau.modeset=0`禁用 nouveau 驱动即可.
- 安装 NVIDIA 显卡驱动可以使用一个相对比较简单的方法, `sudo apt install nvidia-settings`
- 设置显卡驱动配置
	- `lspci |grep VGA`查看显卡硬件信息
	```
  00:02.0 VGA compatible controller: Intel Corporation HD Graphics 630 (rev 04)
  01:00.0 VGA compatible controller: NVIDIA Corporation GP107M [GeForce GTX 1050 Mobile] (rev a1)
	```
	- 备份配置文件 `cp /etc/X11/xorg.conf /etc/ X11/xorg.conf.bak`,而我使用 Debian10 却没有 *xorg.conf*需要创建.添加/修改的内容如下:
	```
   Section "Module"
  Load "modesetting"
  EndSection

  Section "Device"
  Identifier "Device0"
  Driver "nvidia"
  BusID "1:0:0" ##这里根据自己的BusID更改,我的 N 卡是 01:00.0 所以是 1:0:0
  VendorName "NVIDIA Corporation"
  EndSection
	```
	- 创建脚本 `sudo vim /usr/local/bin/nvidia_switch.sh`后赋予运行的权限`sudo chmod +x /usr/local/bin/nvidia_switch.sh`
	```
  #添加内容，其中96为dpi，普通屏幕无需修改，高分辨率的请自行参考资料
  xrandr --setprovideroutputsource modesetting NVIDIA-0
  xrandr --auto
  xrandr --dpi 96
	```
	- 根据 DM 的类型对配置文件进行修改(本次使用测试的为 lightdm),修改`/etc/lightdm/lightdm.conf`并去掉`#displaysetupscript=`前面的'#'修改为:`display-setup-script=/usr/local/bin/nvidia_switch.sh`,保存之后重启 lightdm.`systemctl restart lightdm.service`
- 设置双屏扩展屏设置
```
xrandr --output eDP-1-1 --auto --primary
xrandr --output HDMI-0 --right-of eDP-1-1 --auto
```
