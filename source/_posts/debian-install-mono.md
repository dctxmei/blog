---
title: Debian 安裝 Mono
date: 2017-06-16 17:25:11
tags:
  - Linux
  - Debian
categories: 技術
---

**本文內容已過時！**

### 最初

直接使用 Debian 官方源安裝 Mono
```
$ sudo apt-get install mono
```
與 Mono 官方對比，版本過低。

卸載
```
$ sudo apt-get autoremove --purge "mono*"
```

### 使用 Mono 官方源
```
$ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF
```
出現錯誤
```
Executing: /tmp/apt-key-gpghome.ZqIcif1Acm/gpg.1.sh --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF
gpg: failed to start the dirmngr '/usr/bin/dirmngr': No such file or directory
gpg: connecting dirmngr at '/tmp/apt-key-gpghome.ZqIcif1Acm/S.dirmngr' failed: No such file or directory
gpg: keyserver receive failed: No dirmngr
```
根據關鍵信息 `gpg: keyserver receive failed: No dirmngr`
```
$ sudo apt-cache search -n dirmngr
```
得到
```
dirmngr - GNU privacy guard - network certificate management service
```
安裝軟件
```
$ sudo apt-get install dirmngr
```
再次
```
$ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF
```

#### 添加 beta 源
```
$ echo "deb http://download.mono-project.com/repo/debian beta main" | sudo tee /etc/apt/sources.list.d/mono-official-beta.list
```
```
$ sudo apt-get clean
$ sudo apt-get update
```

#### 完整安裝
```
$ sudo apt-get install mono-complete
```
