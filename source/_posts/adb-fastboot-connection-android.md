---
title: adb/fastboot 连接 Android
date: 2017-09-17 10:03:58
categories: 技术
tags:
  - Debian
  - Android
---

* 本文的相关讨论可前往 debian吧 的 [记录 adb/fastboot 连接 Android](http://tieba.baidu.com/p/5139842856?pid=107626892891&cid=0#107626892891)
<!--more-->

### 安装
```
$ sudo apt install android-tools-adb fastboot
```

### adb
接入开启USB调试的手机前后各输入一次
```
$ lsusb
```
找到设备，比如窝的是“Bus 002 Device 038: ID 271d:0c03”
```
$ sudo vi /etc/udev/rules.d/51-android.rules
```
写入
```
SUBSYSTEM=="usb", ATTR{idVendor}=="271d", ATTR{idProduct}=="0c03", MODE=="0666"
```
```
$ adb kill-server
$ adb devices
```

### fastboot
阔以 adb 连接后
```
$ adb reboot bootloader
```
总之要手机进入 fastboot 模式
```
$ lsusb
```
找到设备，比如窝的是“Bus 002 Device 034: ID 0bb4:0c01 HTC (High Tech Computer Corp.) Dream / ADP1 / G1 / Magic / Tattoo”
```
$ sudo vi /etc/udev/rules.d/51-android.rules
```
写入
```
SUBSYSTEM=="usb", ATTR{idVendor}=="0bb4", ATTR{idProduct}=="0c01", MODE=="0666"
```
重新接入手机并输入
```
$ fastboot devices
```
然后就阔以进行各种作死刷机哩
