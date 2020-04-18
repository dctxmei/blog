---
title: Linux 地狱已满服务器
date: 2017-06-21 12:14:38
categories: 技术
tags:
  - Linux
  - 服务器
---

* 参考[官方 WiKi](https://wiki.nomoreroominhell.com/Dedicated_Server_Setup)

### 安装

#### 建立专有用户
```
$ sudo useradd -m -s /bin/bash nmrihserver
$ sudo passwd nmrihserver
$ sudo su - nmrihserver
```
<!--more-->

#### 为 SteamCMD 创建一个目录
```
$ mkdir steamcmd
$ cd steamcmd
```

#### 下载 SteamCMD
```
$ wget http://media.steampowered.com/client/steamcmd_linux.tar.gz
```

#### 提取 SteamCMD
```
$ tar -zxvf steamcmd_linux.tar.gz
```

#### 为服务器文件创建一个目录
```
$ mkdir /home/nmrihserver/serverfiles
```

#### 下载服务器
* 需要一个Steam 用户名和密码才能使用 SteamCMD 进行身份验证
* 推荐创建一个新的 Steam 用户
* Steam 用户需完成邮箱验证+订阅地狱已满(No More Room in Hell)才可继续

```
$ ./steamcmd.sh +login username password +force_install_dir "/home/nmrihserver/serverfiles" +app_update 317670 validate +quit
```
---
例：有个 nmrihserver 用户名，密码 2nmrihserver
```
$ ./steamcmd.sh +login nmrihserver 2nmrihserver +force_install_dir "/home/nmrihserver/serverfiles" +app_update 317670 validate +quit
```
一切顺利便会向尼的绑定邮箱发送验证码邮件
请等待邮件发送成功并查收邮件后输入验证码
* 下载服务器的过程可能会突然中断，请重复下载命令，会断点续传，可自行写一个脚本以保证下载顺利

#### 64 位发行版
地狱已满服务器需 32 位运行库才能运行
**Ubuntu 64 位**
```
$ sudo apt-get install lib32gcc1
```
**Debian 64 位**
```
$ sudo dpkg --add-architecture i386
$ sudo apt-get clean && sudo apt update
```
```
$ sudo apt-get install lib32gcc1
```
**CentOS 64 位**
```
$ sudo yum install glibc.i686 libstdc++.i686
```
**其它发行版**
其它 64 位发行版请自行解决

#### 启动服务器
```
$ /home/nmrihserver/serverfiles/srcds_run -console -game nmrih -insecure +map nmo_broadway -maxplayers 5
```
关于服务器的命令行参数可参考 Valve Wiki
[https://developer.valvesoftware.com/wiki/Command_Line_Options#Source_Dedicated_Server](https://developer.valvesoftware.com/wiki/Command_Line_Options#Source_Dedicated_Server)

### 解决错误
若出现
```
dlopen failed trying to load:
/home/nmrihserver/.steam/sdk32/steamclient.so
with error:
/home/nmrihserver/.steam/sdk32/steamclient.so: cannot open shared object file: No such file or directory
```
```
$ mkdir /home/nmrihserver/.steam/sdk32
$ ln -s /home/nmrihserver/steamcmd/linux32/steamclient.so /home/nmrihserver/.steam/sdk32/steamclient.so
```

若出现
```
WARNING: Failed to load 32-bit libtinfo.so.5 or libncurses.so.5.
  Please install (lib32tinfo5 / ncurses-libs.i686 / equivalent) to enable readline.
```
**Debian 系**
```
$ sudo apt install lib32tinfo5
```
**CentOS**
```
$ sudo yum install ncurses-libs.i686
```
**其它发行版**
请自行解决
