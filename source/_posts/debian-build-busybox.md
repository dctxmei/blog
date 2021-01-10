---
title: Debian，編譯 BusyBox
date: 2017-07-23 14:09:25
tags:
  - Debian
  - BusyBox
categories: 技術
toc: true
---

* 參考 [編譯Android版busybox](https://github.com/yongce/AndroidDevNotes/blob/master/notes/tools/0006-busybox-android.asc)
* 參考 [Compiling busybox for an ARM processor (Android)](http://www.olafdietsche.de/2015/01/19/compiling-busybox-for-arm)
* 參考 [Ndk編譯Busybox](http://www.cloudchou.com/android/post-701.html)

## 常規方案

### armel

```
# apt install gcc-arm-linux-gnueabi libc6-dev-armel-cross
```

### armhf

```
# apt install gcc-arm-linux-gnueabihf libc6-dev-armhf-cross
```

## 獲取 BusyBox

```
$ git clone git://busybox.net/busybox.git
```

## 配置

```
$ cd busybox
$ make defconfig
```

```
$ vi .config

CONFIG_STATIC=y
# armel
CONFIG_CROSS_COMPILER_PREFIX="arm-linux-gnueabi-"
# armhf
CONFIG_CROSS_COMPILER_PREFIX="arm-linux-gnueabihf-"
```

## 編譯

```
$ make -j4
```

## 下載 Android 源碼

> Android 不使用 GNU libc，使用 bionic libc。

### 安裝 Repo

```
# apt install repo
```

### 添加

```
export REPO_URL="https://mirrors.tuna.tsinghua.edu.cn/git/git-repo/"
```

### 傳統初始化

```
$ mkdir android && cd android
$ repo init -u https://aosp.tuna.tsinghua.edu.cn/platform/manifest -b android-5.1.1_r38
```

### 同步源碼樹

```
$ repo sync
```

## Android-NDK 編譯

```
# apt install google-android-ndk-installer
$ export PATH="/usr/lib/android-ndk:/usr/lib/android-ndk/toolchains/arm-linux-androideabi-4.9/prebuilt/linux-x86_64/bin:$PATH"
```

### 獲取 BusyBox

```
$ git clone git://busybox.net/busybox.git
```

### 配置

```
$ cd busybox
$ make android_ndk_defconfig
```

```
$ vi .config

CONFIG_STATIC=y
CONFIG_SYSROOT="/usr/lib/android-ndk/platforms/android-22/arch-arm"
CONFIG_EXTRA_CFLAGS="-DANDROID -D__ANDROID__ -DSK_RELEASE -nostdlib -msoft-float -mfloat-abi=softfp -mfpu=neon -mthumb -mthumb-interwork -fpic -fno-short-enums -fgcse-after-reload -frename-registers -fuse-ld=bfd"
CONFIG_EXTRA_LDFLAGS="-Xlinker -z -Xlinker muldefs -nostdlib -Xlinker -z -Xlinker nocopyreloc -Xlinker --no-undefined ${SYSROOT}/usr/lib/crtbegin_dynamic.o ${SYSROOT}/usr/lib/crtend_android.o"
CONFIG_EXTRA_LDLIBS="m c gcc"
```

### 編譯

```
$ make -j4
```

**這將遇到各種錯誤，根據錯誤使用**

```
$ make menuconfig
```

**來取消錯誤模塊，並重復編譯，直到所有錯誤模塊全部禁用，編譯成功**

## 說明

```
CONFIG_STATIC=y
```

爲靜態編譯。

```
CONFIG_SYSROOT="/usr/lib/android-ndk/platforms/android-22/arch-arm"
```

這裏的 android-22 爲 API 級別，可隨意更換。

具體請使用：

```
$ ls /usr/lib/android-ndk/platforms
```

查看，並前往 [什幺是 API 級別？](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html?hl=zh-cn#ApiLevels) 和 [平臺版本](https://developer.android.com/about/dashboards/index.html?hl=zh-cn)。
