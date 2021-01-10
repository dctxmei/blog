---
title: 在 Raspberry Pi OS 上部署 Transmission 服務
date: 2020-09-04 20:51:21
tags:
  - Raspberry Pi
  - Raspberry Pi OS
  - Transmission
categories: 技術
toc: true
---

又一次爲 Raspberry Pi OS 進行 Upgrade 後，才發現跑起來的服務實在是少呢，思索一下，不妨再部署個 Transmission 罷。

## 安裝 Transmission

```
$ sudo apt install transmission-cli transmission-daemon
```

## 修改檔案

### 備份檔案

```
$ sudo cp /etc/transmission-daemon/settings.json /etc/transmission-daemon/settings.json.bak
```

### 停止服務

先停止服務才能修改配置檔案，若反之，會在服務停止後被原有配置覆蓋。

```
$ sudo systemctl stop transmission-daemon.service
```

### 正式修改

```
$ sudo vim /etc/transmission-daemon/settings.json

-    "rpc-password": "{b13c6c553b7bc875dadcb772d268f1df5e064c09Xhq8vQHK",
+    "rpc-password": "asldljc553b7bc875dadcb7777fulqI2IuDD7777fulqI2IuD",
-    "rpc-whitelist-enabled": true,
+    "rpc-whitelist-enabled": false,
```

`rpc-password` 內寫下明文密碼，啓動服務後會被哈希值代替。
`rpc-whitelist-enabled` 在一切部署完成後修改回 true，交由 Web Server 反向代理，以後通過 HTTPS 訪問。

### 啓動服務

```
$ sudo systemctl start transmission-daemon.service
```

### 訪問 Web

http://ip:9091/

用戶名是 transmission，密碼爲上述 settings.json 檔案所設。

## Transmission Web Control

Transmission 提供的 Web 實在是太難看了，換用 [Transmission Web Control](https://github.com/ronggang/transmission-web-control)。

該項目提供了一個 [指令碼](https://github.com/ronggang/transmission-web-control/wiki/Linux-Installation) 進行安裝，閱讀後捨棄即可，手動安裝完全沒問題。

### 備份默認 Web 檔案

```
$ sudo cp -r /usr/share/transmission/web/ /usr/share/transmission/web_bak/

```

### 獲取 Transmission Web Control 檔案

當前的 Latest release 爲 v1.6.0-beta2。

```
$ wget -P /tmp/ https://github.com/ronggang/transmission-web-control/archive/v1.6.0-beta2.tar.gz
```

### Original UI 功能

原有的 Web 檔案無需移除，Transmission Web Control 提供了 Original UI 功能，仍可繼續使用。

```
$ sudo install /usr/share/transmission/web/index.html /usr/share/transmission/web/index.original.html
```

### 解開 Transmission Web Control 檔案

```
$ cd /tmp/
$ tar -xvf v1.6.0-beta2.tar.gz
```

### 設定檔案權限

Transmission Web Control 檔案中，目錄的默認權限是 700，這會導致訪問 Web 呈現空白（因爲 Owner 沒有權限）。

```
$ cd transmission-web-control-1.6.0-beta2/
$ find src -type d -exec chmod 755 {} \;
```

### 正式同步

```
$ sudo rsync -vrP src/ /usr/share/transmission/web/
```

然後重新訪問 Web 即可，界面更加美觀易用啦。
