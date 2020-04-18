---
title: 在 Debian 安装网易云音乐
date: 2017-05-31 21:24:52
categories: 技术
tags:
  - Debian
toc: true
---

### 1.0.0-1

* stable 无须修改软件包依赖可直接安装 ubuntu14.04 版本。
* testing/unstable 本文修改 debian15 版本软件包依赖并安装。
<!--more-->
* 其它一些基于 Debian 的发行版，如 Kali Linux 也可参考本文方案。
* 本文的相关讨论可前往 debian吧 的 [在 Debian 安装网易云音乐](http://tieba.baidu.com/p/5078785088)
* testing/unstable 欲修改 ubuntu 版本软件包依赖可参考 FindHao 的 [debian安装网易云解决依赖问题](https://www.findhao.net/easycoding/1875)

#### 准备
```
$ export architecture="" # 自行输入 i386/amd64
$ mkdir netease-cloud-music
$ cd netease-cloud-music
$ wget http://s1.music.126.net/download/pc/netease-cloud-music_1.0.0-1_${architecture}_deepin15.deb
```

#### 创建所需目录
```
$ mkdir -p {extract/DEBIAN,build}
```

#### 将主控信息解压
```
$ sudo dpkg -e netease-cloud-music_1.0.0-1_${architecture}_deepin15.deb extract/DEBIAN
```

#### 将所有文件解压
```
$ sudo dpkg -x netease-cloud-music_1.0.0-1_${architecture}_deepin15.deb extract
```

#### 以 qt5-gtk-platformtheme 代替 libqt5libqgtk2
```
$ sudo sed 's/libqt5libqgtk2/qt5-gtk-platformtheme/' -i extract/DEBIAN/control
```

#### 打包
```
$ dpkg -b extract build
```

#### 安装
```
$ sudo dpkg -i build/netease-cloud-music_1.0.0-1_${architecture}.deb
$ sudo apt-get -f install
```

---

### 1.0.0-2
#### 环境
```
$ sudo debootstrap stable stable-root http://ftp.cn.debian.org/debian/
$ sudo chroot stable-root /bin/bash
```

|测试        |stable   |testing  |unstable |
|:-----------|:-------:|:-------:|:-------:|
|deepin15    |成功     |成功     |成功     |
|ubuntu16.04 |失败     |失败     |失败     |
|ubuntu14.04 |失败     |失败     |失败     |

---

### 1.1.0
[http://tieba.baidu.com/p/5481843273](http://tieba.baidu.com/p/5481843273)
