---
title: Debian 安装 Virtual Machine Manager
date: 2017-10-01 20:02:42
categories: 技术
tags:
  - Debian
---

只是觉得很有趣啦，由红帽开发！
<!--more-->

### 安装
```
$ sudo apt install dnsmasq gir1.2-spice-client-gtk-3.0 libvirt-daemon-system qemu-kvm qemu-utils virt-manager
```

#### 添加用户至组
```
$ sudo usermod -a -G libvirt mei
```

### 卸载并清理
```
$ sudo apt autoremove --purge dnsmasq gir1.2-spice-client-gtk-3.0 libvirt-daemon-system qemu-kvm qemu-utils virt-manager
$ sudo rm -rf /var/lib/libvirt /etc/libvirt ~/.cache/virt-manager
```
