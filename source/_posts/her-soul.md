---
title: 她的靈魂
date: 2020-04-20 23:09:49
tags:
  - Arch Linux
  - LVM
  - LUKS
  - rsync
categories: 技術
toc: true
---

## 引言

我使用 Arch Linux，已經數年了，期間從未重裝過作業系統，她仍是當年的她。

我愛她的靈魂，悉心呵護。

不過，現今，她需要一個新的載體。

## 填充

```
DEVICE="/dev/sdX"
PASS="$(tr -cd '[:alnum:]' < /dev/urandom | head -c 128)"
openssl enc -aes-256-ctr -pass pass:"$PASS" -nosalt < /dev/zero | dd bs=64K ibs=64K of=$DEVICE status=progress
```

## 分區

```
# parted /dev/sdX

GNU Parted 3.3
Using /dev/sdX
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted) mklabel gpt
(parted) mkpart GRUB 1MiB 2MiB
(parted) set 1 bios_grub on
(parted) mkpart ESP 2MiB 513MiB
(parted) set 2 boot on
(parted) mkpart primary 513MiB 100%
(parted) quit
Information: You may need to update /etc/fstab.
```

```
# cryptsetup luksFormat /dev/sdX3
# cryptsetup open /dev/sdX3 cryptlvm_new
# pvcreate /dev/mapper/cryptlvm_new
# vgcreate "$UUID" /dev/mapper/cryptlvm
# lvcreate -L XG "$UUID" -n root
# lvcreate -l 100%FREE "$UUID" -n swap
```

## 格式

```
# mkfs.fat -F 32 /dev/sdX2
# mkfs.xfs /dev/"$UUID"/root
# mkswap /dev/"$UUID"/swap
```

## 掛載

```
# mkdir /mnt/new/
# mount /dev/"$UUID"/root /mnt/new/
# mkdir /mnt/new/boot/
# mount /dev/sdX2 /mnt/new/boot/
```

## 同步

```
rsync -aAhHiKPvX / /mnt/new/ --delete --exclude={/dev/*,/proc/*,/sys/*,/tmp/*,/run/*,/mnt/new/*,/media/*}
```

## 後續

修改 `/etc/fastab` 和 Boot 一類，無需多言。

她仍是當年的她。
