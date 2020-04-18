---
title: Oh My Debian
date: 2017-03-23 05:59:16
categories: 技术
tags:
  - Debian
toc: true
---

### 准备
下载 [Debian testing multi-arch iso-cd](http://cdimage.debian.org/cdimage/weekly-builds/multi-arch/iso-cd/debian-testing-amd64-i386-netinst.iso)
<!--more-->
烧录镜像至 U 盘：
```
# dd if=debian-testing-amd64-i386-netinst.iso of=/dev/xx bs=512k
```
U 盘启动，最小化安装 Debian
<!--more-->

### 从零开始
```
su -
```

#### 修改启动时间
```
# sed 's/GRUB_TIMEOUT=5/GRUB_TIMEOUT=2/' -i /etc/default/grub
# update-grub
```

#### 接入智能手机开启 USB 网络共享
```
# ip link set enp0s20u2u1 up
# dhclient enp0s20u2u1
```

#### 开启 32 位支持
```
# dpkg --add-architecture i386
```

#### 设定镜像源
```
# vi /etc/apt/sources.list
```
```
deb http://ftp.cn.debian.org/debian/ sid contrib main non-free
deb-src http://ftp.cn.debian.org/debian/ sid contrib main non-free
```
```
# apt-get clean
# apt-get update
```

#### 安装 sudo
```
# apt-get install sudo
# echo -e '\nmei\tALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers
```
```
# logout
```

#### 安装闭源网卡驱动
```
$ lspci | grep Network
```
```
03:00.0 Network controller: Realtek Semiconductor Co., Ltd. RTL8723BE PCIe Wireless Network Adapter
```
```
$ sudo apt-get install firmware-realtek
```

#### 开启无线网卡
```
$ sudo apt-get install rfkill
$ sudo rfkill list
$ sudo rfkill unblock IDENTIFIER
$ sudo ip link set wlp3s0 up
```

#### 关闭 USB 网络共享
```
$ sudo dhclient -r
$ sudo ip link set enp0s20u2u1 down
```

#### 连接 WiFi
```
$ wpa_passphrase XXXX XXXXXXXXXXXXXX | sudo tee /etc/wpa_supplicant/wpa_supplicant.conf
$ sudo dhclient wlp3s0
```

### 搭建桌面环境
#### 目录
```
$ cd
$ mkdir download experiment file image music video
```
#### 基本
* conky：高度可配置的系统监控器
* feh：基于 imlib2 的图像查看器
* fonts-font-awesome：使用推特引导图标式字体设计
* i3lock：改进的屏幕锁
* i3-wm：改进的动态平铺窗口管理器
* rxvt-unicode-256color
* scrot：命令行屏幕捕获实用程序
* suckless-tools：简约的窗口管理器简单的命令
* xcompmgr：X 组合管理器
* xinit：X 服务器初始化工具
* xserver-xorg-input-libinput：键盘、鼠标、触摸板的驱动程序
* xserver-xorg-video-intel：Intel 开源驱动程序

#### 声音
* alsa-utils：配置和使用 ALSA 的实用程序
* pulseaudio

#### 主题
```
$ sudo vi /etc/default/grub
```
```
GRUB_BACKGROUND="/home/mei/image/grub.png"
```
```
$ sudo update-grub
```
* arc-theme
* breeze-cursor-theme
[Flat Remix](https://github.com/daniruiz/Flat-Remix) - icon theme
* gtk2-engines-pixbuf
* lxappearance
* qt4-qtconfig

#### 编译
* i3-gaps
* polybar

```
$ sudo apt install ~
```
