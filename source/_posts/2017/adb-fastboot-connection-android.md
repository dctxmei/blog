---
title: ADB/Fastboot 連接 Android
date: 2017-09-17 10:03:58
tags:
  - Debian
  - ADB
  - Fastboot
  - Android
categories: 技術
---

## 安裝

```
# apt install android-tools-adb fastboot
```

## ADB

在接入，開啓 USB 調試的手機前後，各輸入一次：

```
$ lsusb
```

找到設備，比如我的是「Bus 002 Device 038: ID 271d:0c03」

```
# vi /etc/udev/rules.d/51-android.rules

SUBSYSTEM=="usb", ATTR{idVendor}=="271d", ATTR{idProduct}=="0c03", MODE=="0666"
```

```
$ adb kill-server
$ adb devices
```

## fastboot

可以在 ADB 連接後，執行：

```
$ adb reboot bootloader
```

總之，要手機進入 Fastboot 模式。

```
$ lsusb
```

找到設備，比如我的是「Bus 002 Device 034: ID 0bb4:0c01 HTC (High Tech Computer Corp.) Dream / ADP1 / G1 / Magic / Tattoo」。

```
# vi /etc/udev/rules.d/51-android.rules

SUBSYSTEM=="usb", ATTR{idVendor}=="0bb4", ATTR{idProduct}=="0c01", MODE=="0666"
```

重新接入手機，執行：

```
$ fastboot devices
```

就可進行刷機了。
