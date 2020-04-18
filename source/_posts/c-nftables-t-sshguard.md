---
title: 配置 nftables 以使用 sshguard
date: 2018-10-20 18:19:45
categories: 技术
tags:
  - Arch Linux
  - nftables
  - SSH
---

sshguard 是一个保护 SSH 和其他服务免受暴力攻击的守护进程
<!--more-->

* 参考 [Sshguard#nftables](https://wiki.archlinux.org/index.php/Sshguard#nftables) Wiki

在已使用 nftables 的基础上，安装 sshguard
```
$ sudo pacman -S sshguard
```

编辑 /etc/sshguard.conf 并更改 BACKEND
```
/etc/sshguard.conf

BACKEND="/usr/lib/sshguard/sshg-fw-nft-sets"
```

编辑 sshguard.service 以便不运行 iptables
```
$ sudo systemctl edit sshguard.service
```
```
[Service]
ExecStartPre=
```

启动服务
```
$ sudo systemctl enable sshguard.service --now
```
