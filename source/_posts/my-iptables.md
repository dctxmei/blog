---
title: 我的 iptables
date: 2017-10-06 15:17:07
categories: 技术
tags:
  - Debian
  - iptables
---

新式的数据包过滤框架，从 Linux 内核 3.13 版本开始可用，旨在替代现用的 iptables 框架——nftables
本博客相关文档 [我的 nftables](https://blog.dctxmei.me/2017/10/06/my-nftables.html)
<!--more-->

### 安装
```
$ sudo apt install iptables-persistent
```

### 规则
```
$ sudo iptables -P FORWARD DROP
$ sudo iptables -P OUTPUT ACCEPT
$ sudo iptables -P INPUT DROP
```
本地已建立连接的流量，或与这些连接相关的新的有效流量都将通过
```
$ sudo iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
$ sudo iptables -A INPUT -m conntrack --ctstate INVALID -j DROP
$ sudo iptables -A INPUT -i lo -j ACCEPT
```
保存
```
$ sudo iptables-save | sudo tee /etc/iptables/rules.v4
```
