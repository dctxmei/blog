---
title: Oh My Debian
date: 2017-03-23 05:59:16
tags:
  - Debian
categories: 技術
toc: true
---

**本文內容已過時！**

### 準備
下載 [Debian testing multi-arch iso-cd](http://cdimage.debian.org/cdimage/weekly-builds/multi-arch/iso-cd/debian-testing-amd64-i386-netinst.iso)
燒錄鏡像至 U 盤：
```
# dd if=debian-testing-amd64-i386-netinst.iso of=/dev/xx bs=512k
```
U 盤啟動，最小化安裝 Debian

### 從零開始
```
su -
```

#### 修改啟動時間
```
# sed 's/GRUB_TIMEOUT=5/GRUB_TIMEOUT=2/' -i /etc/default/grub
# update-grub
```

#### 接入智能手機開啟 USB 網絡共享
```
# ip link set enp0s20u2u1 up
# dhclient enp0s20u2u1
```

#### 開啟 32 位支持
```
# dpkg --add-architecture i386
```

#### 設定鏡像源
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

#### 安裝 sudo
```
# apt-get install sudo
# echo -e '\nmei\tALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers
```
```
# logout
```

#### 安裝閉源網卡驅動
```
$ lspci | grep Network
```
```
03:00.0 Network controller: Realtek Semiconductor Co., Ltd. RTL8723BE PCIe Wireless Network Adapter
```
```
$ sudo apt-get install firmware-realtek
```

#### 開啟無線網卡
```
$ sudo apt-get install rfkill
$ sudo rfkill list
$ sudo rfkill unblock IDENTIFIER
$ sudo ip link set wlp3s0 up
```

#### 關閉 USB 網絡共享
```
$ sudo dhclient -r
$ sudo ip link set enp0s20u2u1 down
```

#### 連接 WiFi
```
$ wpa_passphrase XXXX XXXXXXXXXXXXXX | sudo tee /etc/wpa_supplicant/wpa_supplicant.conf
$ sudo dhclient wlp3s0
```

### 搭建桌面環境
#### 目錄
```
$ cd
$ mkdir download experiment file image music video
```
#### 基本
* conky：高度可配置的系統監控器
* feh：基於 imlib2 的圖像查看器
* fonts-font-awesome：使用推特引導圖標式字體設計
* i3lock：改進的屏幕鎖
* i3-wm：改進的動態平鋪窗口管理器
* rxvt-unicode-256color
* scrot：命令行屏幕捕獲實用程序
* suckless-tools：簡約的窗口管理器簡單的命令
* xcompmgr：X 組合管理器
* xinit：X 伺服器初始化工具
* xserver-xorg-input-libinput：鍵盤、鼠標、觸摸板的驅動程序
* xserver-xorg-video-intel：Intel 開源驅動程序

#### 聲音
* alsa-utils：配置和使用 ALSA 的實用程序
* pulseaudio

#### 主題
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

#### 編譯
* i3-gaps
* polybar

```
$ sudo apt install ~
```
