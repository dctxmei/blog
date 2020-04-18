---
title: Debian 编译 BusyBox
date: 2017-07-23 14:09:25
categories: 技术
tags:
  - Debian
toc: true
---

* 参考 [编译Android版busybox](https://github.com/yongce/AndroidDevNotes/blob/master/notes/tools/0006-busybox-android.asc)
* 参考 [Compiling busybox for an ARM processor (Android)](http://www.olafdietsche.de/2015/01/19/compiling-busybox-for-arm)
* 参考 [Ndk编译Busybox](http://www.cloudchou.com/android/post-701.html)
<!--more-->

### 常规方案
armel
```
$ sudo apt install gcc-arm-linux-gnueabi libc6-dev-armel-cross
```
armhf
```
$ sudo apt install gcc-arm-linux-gnueabihf libc6-dev-armhf-cross
```

#### 获取 BusyBox
```
$ git clone git://busybox.net/busybox.git
```

#### 配置
```
$ cd busybox
$ make defconfig
```
```
$ vi .config
```
```
CONFIG_STATIC=y
# armel
CONFIG_CROSS_COMPILER_PREFIX="arm-linux-gnueabi-"
# armhf
CONFIG_CROSS_COMPILER_PREFIX="arm-linux-gnueabihf-"
```

#### 编译
```
$ make -j4
```

---

> Android 不使用 GNU libc，使用 bionic libc。

### 下载 Android 源码
#### 安装 repo
```
$ sudo apt install repo
```
添加
```
export REPO_URL="https://mirrors.tuna.tsinghua.edu.cn/git/git-repo/"
```
#### 传统初始化
```
$ mkdir android && cd android
$ repo init -u https://aosp.tuna.tsinghua.edu.cn/platform/manifest -b android-5.1.1_r38
```
同步源码树
```
$ repo sync
```

### Android-NDK 编译
```
$ sudo apt install google-android-ndk-installer
$ export PATH="/usr/lib/android-ndk:/usr/lib/android-ndk/toolchains/arm-linux-androideabi-4.9/prebuilt/linux-x86_64/bin:$PATH"
```

#### 获取 BusyBox
```
$ git clone git://busybox.net/busybox.git
```

#### 配置
```
$ cd busybox
$ make android_ndk_defconfig
```
```
$ vi .config
```
```
CONFIG_STATIC=y
CONFIG_SYSROOT="/usr/lib/android-ndk/platforms/android-22/arch-arm"
CONFIG_EXTRA_CFLAGS="-DANDROID -D__ANDROID__ -DSK_RELEASE -nostdlib -msoft-float -mfloat-abi=softfp -mfpu=neon -mthumb -mthumb-interwork -fpic -fno-short-enums -fgcse-after-reload -frename-registers -fuse-ld=bfd"
CONFIG_EXTRA_LDFLAGS="-Xlinker -z -Xlinker muldefs -nostdlib -Xlinker -z -Xlinker nocopyreloc -Xlinker --no-undefined ${SYSROOT}/usr/lib/crtbegin_dynamic.o ${SYSROOT}/usr/lib/crtend_android.o"
CONFIG_EXTRA_LDLIBS="m c gcc"
```

#### 编译
```
$ make -j4
```
**这将遇到各种错误，根据错误使用**
```
$ make menuconfig
```
**来取消错误模块，并重复编译，直到所有错误模块全部禁用，编译成功**

---

### 说明
```
CONFIG_STATIC=y
```
为静态编译

```
CONFIG_SYSROOT="/usr/lib/android-ndk/platforms/android-22/arch-arm"
```
这里的 android-22 为 API 级别，可随意更换
具体请使用
```
$ ls /usr/lib/android-ndk/platforms
```
查看，并前往[什么是 API 级别？](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html?hl=zh-cn#ApiLevels)和[平台版本](https://developer.android.com/about/dashboards/index.html?hl=zh-cn)
