---
title: Debian 安裝 Infinality
date: 2017-04-07 20:41:51
tags:
  - Debian
categories: 技術
---

**本文內容已過時！**

### 如何在 Debian 安裝 Infinality 字體渲染？

#### 保證你的命令及網絡能暢通無阻

```
sudo -l
lantern
```

#### 下載所需文件
```
$ wget 'http://download.savannah.gnu.org/releases/freetype/freetype-2.5.0.1.tar.bz2'
$ wget 'https://raw.githubusercontent.com/chenxiaolong/Debian-Packages/master/freetype-infinality/debian/patches/freetype-enable-subpixel-hinting-infinality-20120403-01.patch'
$ wget -O 'fontconfig-infinality-master.zip' 'https://github.com/Infinality/fontconfig-infinality/archive/master.zip'
$ wget 'https://raw.githubusercontent.com/chenxiaolong/Debian-Packages/master/freetype-infinality/debian/patches/infinality-settings.sh'
```

#### 解壓 freetype-2.5.0.1.tar.bz2，打入次像素暗示補丁並編譯
```
$ tar -jxvf freetype-2.5.0.1.tar.bz2
$ cd freetype-2.5.0.1
$ patch -Np1 -i ../freetype-enable-subpixel-hinting-infinality-20120403-01.patch
```

#### 編譯開始前，或許需要安裝一些依賴
```
$ sudo apt-get install libbz2-dev libharfbuzz-dev libpng-dev zlib1g-dev
```

#### 開始編譯
```
$ ./configure
$ sudo make
```

#### 安裝 freetype 庫
```
$ sudo rm /usr/lib/x86_64-linux-gnu/libfreetype.so.6*
$ sudo install -m 644 objs/.libs/libfreetype.so.6.10.2 /usr/lib/x86_64-linux-gnu
$ sudo ln -s /usr/lib/x86_64-linux-gnu/libfreetype.so.6.10.2 /usr/lib/x86_64-linux-gnu/libfreetype.so.6
$ sudo ln -s /usr/lib/x86_64-linux-gnu/libfreetype.so.6.10.2 /usr/lib/x86_64-linux-gnu/libfreetype.so
$ echo 'libfreetype6 hold' | sudo dpkg --set-selections
$ echo 'libfreetype6-dev hold' | sudo dpkg --set-selections
```

#### 解壓 fontconfig-infinality-master.zip 並進行安裝
```
$ cd ..
$ sudo unzip fontconfig-infinality-master.zip
$ cd fontconfig-infinality-master
$ sudo mkdir /etc/fonts/infinality
$ sudo mv conf.avail/* /etc/fonts/conf.avail
$ sudo mv conf.d/* /etc/fonts/conf.d
$ sudo mv infinality/{conf*,inf*,styles.conf.avail} /etc/fonts/infinality
$ cd ..
$ sudo mv infinality-settings.sh /etc/profile.d
```

---

### 清理
```
$ sudo rm -rf freetype* fontconfig*
```

6.修改渲染方案
```
$ sudo bash /etc/fonts/infinality/infctl.sh setstyle
```
or
```
$ sudo vi /etc/profile.d/infinality-settings.sh
```

* 如何卸載？
```
$ sudo rm -r /usr/lib/x86_64-linux-gnu/libfreetype*/etc/fonts/conf*/52* /etc/fonts/infinality /etc/profile.d/infinality-settings.sh
$ echo 'libfreetype6 install' | sudo dpkg --set-selections
$ echo 'libfreetype6-dev install' | sudo dpkg --set-selections
$ sudo apt-get --reinstall install libfreetype6
$ sudo apt-get --reinstall install libfreetype6-dev
```
