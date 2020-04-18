---
title: 个人「Arch Linux」安装手册
date: 2018-02-07 20:03:39
categories: 技术
tags:
  - Arch Linux
---

只是送给自己的安装手册啦
<!--more-->

## 安装准备
### 连接到因特网
验证网络是否正常
```
# ping -c 3 archlinux.org
```

### 更新系统时间
用 systemd-timesyncd 确保系统时间是正确的
```
# timedatectl set-ntp true
```
检查服务状态
```
# timedatectl status
```

### 建立硬盘分区
```
# parted /dev/sda
(parted) mklabel gpt
(parted) mkpart ESP fat32 1MiB 513MiB
(parted) set 1 boot on
(parted) mkpart primary xfs 513MiB 20.5GiB
(parted) mkpart primary xfs 20.5GiB 32.5GiB
(parted) mkpart primary xfs 32.5GiB 38.5GiB
(parted) mkpart primary linux-swap 38.5GiB 42.5GiB
(parted) mkpart primary xfs 42.5GiB 100%
(parted) quit
```

### 格式化分区
```
# mkfs.vfat -F 32 /dev/sda1
# mkfs.xfs /dev/sda2
# mkfs.xfs /dev/sda3
# mkfs.xfs /dev/sda4
# mkswap /dev/sda5
# mkfs.xfs /dev/sda6
```

### 挂载分区
```
# mount /dev/sda2 /mnt
# mkdir /mnt/boot
# mount /dev/sda1 /mnt/boot
# mkdir /mnt/var
# mount /dev/sda3 /mnt/var
# mkdir /mnt/srv
# mount /dev/sda4 /mnt/srv
# mkdir /mnt/home
# swapon /dev/sda5
# mount /dev/sda6 /mnt/home
```

## 安装
### 选择镜像
```
# vim /etc/pacman.d/mirrorlist
```

### 安装基本系统
```
# pacstrap -i /mnt base base-devel
```

## 配置系统
### Fstab
```
# genfstab -U /mnt >> /mnt/etc/fstab
```

### Chroot
```
# arch-chroot /mnt
```

### 时区
```
# ln -sf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime
```
设置时间标准为 UTC，并调整时间漂移
```
# hwclock --systohc
```

### Locale
```
# vi /etc/locale.gen

en_US.UTF-8 UTF-8
zh_CN.UTF-8 UTF-8
zh_TW.UTF-8 UTF-8
```
执行 locale-gen 以生成 locale 讯息
```
# locale-gen
```
创建 locale.conf 并提交的本地化选项
```
# echo LANG=en_US.UTF-8 > /etc/locale.conf
```

### 主机名
```
# echo dictator > /etc/hostname
```
添加对应的信息到 hosts
```
vi /etc/hosts

# Static table lookup for hostnames.
# See hosts(5) for details.
127.0.0.1       localhost.localdomain   localhost
::1             localhost.localdomain   localhost
127.0.1.1       dictator.localdomain    dictator
```

### Root 密码
```
# passwd
```

### 配置 initramfs 以支持休眠
```
vi /etc/mkinitcpio.conf

HOOKS=(base udev autodetect modconf block filesystems keyboard resume fsck)
```

### 添加 lz4 lz4_compress 至 MODULES 以由内核尽早加载 lz4 模块
```
vi /etc/mkinitcpio.conf

MODULES=(lz4 lz4_compress)
```

### 安装引导程序
```
# bootctl install
```
```
# vi /boot/loader/loader.conf

default arch
timeout 0
editor no
```
```
# blkid -s PARTUUID -o value /dev/sda2 >> /boot/loader/entries/arch.conf
```
必需的内核参数
```
# vi /boot/loader/entries/arch.conf

title Arch Linux
linux /vmlinuz-linux-zen
initrd /intel-ucode.img
initrd /initramfs-linux-zen.img
options root=PARTUUID=92fabd34-0a2e-46a7-aad2-b8b464ae30e9
options resume=PARTUUID=f1ba754d-b736-4824-8e0a-fad3f53d07e0
options zswap.enabled=1 zswap.compressor=lz4 zswap.max_pool_percent=25 zswap.zpool=z3fold
```
```
# bootctl update
```

## 重启
```
# exit
# umount -R /mnt
# reboot
```

## LUKS + LVM2

特殊操作:)

```
# parted /dev/sda
(parted) mklabel gpt
(parted) mkpart ESP 1MiB 513MiB
(parted) set 1 boot on
(parted) mkpart primary 513MiB 100%
(parted) quit
```

```
# cryptsetup luksFormat --type luks2 /dev/sda2
```

```
# cryptsetup open /dev/sda2 cryptlvm
```

```
# pvcreate /dev/mapper/cryptlvm
```

```
# vgcreate dctxmei /dev/mapper/cryptlvm
```

```
# lvcreate -L 4G dctxmei -n swap
# lvcreate -l 100%FREE dctxmei -n root
```

```
# mkfs.xfs /dev/dctxmei/root
# mkfs.fat -F 32 /dev/sda1
# mkswap /dev/dctxmei/swap
```

```
# mount /dev/dctxmei/root /mnt
# mkdir /mnt/boot
# mount /dev/sda1 /mnt/boot
# swapon /dev/dctxmei/swap
```

```
vi /etc/mkinitcpio.conf

HOOKS=(base udev autodetect keyboard keymap consolefont modconf block encrypt lvm2 filesystems keyboard resume fsck)
```

```
# vi /boot/loader/entries/arch.conf

title Arch Linux
linux /vmlinuz-linux-zen
initrd /initramfs-linux-zen.img
initrd /intel-ucode.img
options cryptdevice=UUID=device-UUID:cryptlvm
options root=/dev/dctxmei/root
options resume=/dev/dctxmei/swap
options zswap.enabled=1 zswap.compressor=lz4 zswap.max_pool_percent=25 zswap.zpool=z3fold
```

开启 LUKS 对 TRIM 的支持

```
options cryptdevice=UUID=device-UUID:cryptlvm:allow-discards
```
