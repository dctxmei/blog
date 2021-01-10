---
title: ALA 實際應用
date: 2018-08-19 11:17:16
tags:
  - Arch Linux
  - 伺服器
  - ALA
categories: 技術
---

對於在伺服器使用 Arch Linux 且過於懶惰的我來說，滾動更新簡直就是噩夢。

## 過去式操作

### 鎖定

鎖定 `/boot/` 目錄，使內核無法成功更新，可免除每次更新內核後，進行重啓的習慣：

```
# chattr +i /boot/
```

## 需要更新時該怎幺做呢？

假設使用 `linux` 內核：

```
# chattr -i /boot/
# pacman -S linux
# chattr +i /boot/
# shutdown -r now
```

### Arch Linux Archive

參考 Arch Linux Wiki：[ALA](https://wiki.archlinux.org/index.php/Arch_Linux_Archive)。

在 `/etc/pacman.d/mirrorlist` 中寫入

```
Server = https://archive.archlinux.org/repos/month/$repo/os/$arch
```

其只會同步每月 1 日的倉庫內容，實現類似 Stable 式的滾動更新。
