---
title: KVM 使用，Debian
date: 2017-07-05 01:28:52
categories: 技术
tags:
  - Debian
---

KVM 一生推！
<!--more-->

若出现 `parse_vt_settings: Cannot open /dev/tty0 (Permission denied)`
```
$ sudo apt install xserver-xorg-legacy
```

---

使用 Ctrl+Alt+2 切换到 KVM（Qemu）控制台
输入 sendkey ctrl-alt-f2 并回车
使用 Ctrl+Alt+1 切换回虚拟系统，现在应当在 tty2

```
$ sudo chvt 2
```
效果相同
