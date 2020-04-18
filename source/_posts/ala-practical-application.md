---
title: ALA 实际应用
date: 2018-08-19 11:17:16
categories: 技术
tags:
  - Arch Linux
  - 服务器
---

对于在服务器使用 Arch Linux 且懒癌晚期的我来说，滚动更新简直就是噩梦😂
<!--more-->

### 过去式操作
#### 锁定
锁定 /boot/ 目录，使内核无法成功更新，自然可免除每次更新内核后的优良习惯（重启）
```
# chattr +i /boot/
```

#### 需要更新时该怎么做呢？
假设使用 linux 内核
```
# chattr -i /boot/
# pacman -S linux
# chattr +i /boot/
# shutdown -r now
```

依然很麻烦，但是，我找到了 [ALA](https://wiki.archlinux.org/index.php/Arch_Linux_Archive)
### Arch Linux Archive
Arch Linux 提供每月安装镜像，因此我认为这一天的仓库是稳定的（胡扯

在 `/etc/pacman.d/mirrorlist` 中写入
```
Server = https://archive.archlinux.org/repos/month/$repo/os/$arch
```

此后更新将只会同步每月 1 日的仓库内容，实现类 Stable 滚动更新😆

可以考虑使用 `cronie` 来进行每月 2 日（可保证仓库已同步）自动更新 + 重启
```
* * 2 * * pacman -Syu --noconfirm && pacman -Fy --noconfirm && shutdown -r now
```
