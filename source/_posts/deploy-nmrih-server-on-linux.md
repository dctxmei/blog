---
title: 在 Linux 部署地獄已滿伺服器
date: 2017-06-21 12:14:38
tags:
  - Linux
  - No More Room in Hell
  - 地獄已滿
  - 伺服器
categories: 技術
toc: true
---

* 參考 [官方 WiKi](https://wiki.nomoreroominhell.com/Dedicated_Server_Setup)

## 建立專有用戶

```
# useradd -m -s /bin/bash nmrihserver
# passwd nmrihserver
# su - nmrihserver
```

## 為 SteamCMD 創建一個目錄

```
$ mkdir steamcmd
$ cd steamcmd
```

## 下載 SteamCMD

```
$ wget http://media.steampowered.com/client/steamcmd_linux.tar.gz
```

## 提取 SteamCMD

```
$ tar -zxvf steamcmd_linux.tar.gz
```

## 為伺服器文件創建一個目錄

```
$ mkdir /home/nmrihserver/serverfiles/
```

## 下載伺服器數據

對於 64 位發行版，地獄已滿需 32 位運行庫才可運行。

### Debian

```
# dpkg --add-architecture i386
# apt-get clean
# apt-get update
```

```
# apt-get install lib32gcc1
```

### CentOS

```
# yum install glibc.i686 libstdc++.i686
```

### 正式下載

* 需要一個Steam 用戶名和密碼才能使用 SteamCMD 進行身份驗證
* 推薦創建一個新的 Steam 用戶
* Steam 用戶需完成郵箱驗證 + 訂閱地獄已滿（No More Room in Hell）才可繼續

```
$ ./steamcmd.sh +login username password +force_install_dir "/home/nmrihserver/serverfiles" +app_update 317670 validate +quit
```

例：有個 nmrihserver 用戶名，密碼 2nmrihserver

```
$ ./steamcmd.sh +login nmrihserver 2nmrihserver +force_install_dir "/home/nmrihserver/serverfiles" +app_update 317670 validate +quit
```

一切順利，便會向你的綁定郵箱發送含有驗證碼的郵件。

請等待郵件發送成功，並在查收郵件後輸入驗證碼。

* 下載伺服器的過程可能會突然中斷，請重複下載命令，會斷點續傳，可自行寫一個腳本以保證下載順利。

## 啟動伺服器

```
$ /home/nmrihserver/serverfiles/srcds_run -console -game nmrih -insecure +map nmo_broadway -maxplayers 5
```

關於伺服器的命令行參數可參考 Valve Wiki。

[https://developer.valvesoftware.com/wiki/Command_Line_Options#Source_Dedicated_Server](https://developer.valvesoftware.com/wiki/Command_Line_Options#Source_Dedicated_Server)

## 解決錯誤

### 問題 1

```
dlopen failed trying to load:
/home/nmrihserver/.steam/sdk32/steamclient.so
with error:
/home/nmrihserver/.steam/sdk32/steamclient.so: cannot open shared object file: No such file or directory
```

### 方案 1

```
$ mkdir /home/nmrihserver/.steam/sdk32/
$ ln -s /home/nmrihserver/steamcmd/linux32/steamclient.so /home/nmrihserver/.steam/sdk32/steamclient.so
```

### 問題 2

```
WARNING: Failed to load 32-bit libtinfo.so.5 or libncurses.so.5.
  Please install (lib32tinfo5 / ncurses-libs.i686 / equivalent) to enable readline.
```

### 方案 2

Debian：

```
# apt install lib32tinfo5
```

CentOS：

```
# yum install ncurses-libs.i686
```
