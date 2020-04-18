---
title: 我的 nftables
date: 2017-10-06 20:48:30
categories: 技术
tags:
  - Debian
  - nftables
---

* 参考 [Nftables HOWTO 中文翻译](https://farkasity.gitbooks.io/nftables-howto-zh/content/)
* 参考 [Arch Linux Wiki Nftables](https://wiki.archlinux.org/index.php/nftables)
<!--more-->

### 安装
```
$ sudo apt install nftables
```

### 规则
```
$ sudo nft add table ip filter
$ sudo nft add chain ip filter input { type filter hook input priority 0 \; policy drop \; \}
```
本地已建立连接的流量，或与这些连接相关的新的有效流量都将通过
```
$ sudo nft add rule ip filter input ct state established,related accept
$ sudo nft add rule ip filter input ct state invalid drop
$ sudo nft add rule ip filter input iif lo accept
```
保存
```
$ sudo nft list table filter
```
将输出内容写入 `/etc/nftables.conf`
大致如下
```
#!/usr/sbin/nft -f

flush ruleset

table ip filter {
    chain input {
        type filter hook input priority 0; policy drop;
        ct state established,related accept
        ct state invalid drop
        iif "lo" accept
    }
}
```
开启开机自启
```
$ sudo systemctl enable nftables.service
```
