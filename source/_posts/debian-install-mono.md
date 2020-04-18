---
title: Debian 安装 Mono
date: 2017-06-16 17:25:11
categories: 技术
tags:
  - Linux
  - Debian
---

### 最初
直接使用 Debian 官方源安装 Mono
```
$ sudo apt-get install mono
```
<!--more-->
与 Mono 官方对比，版本过低。

卸载
```
$ sudo apt-get autoremove --purge "mono*"
```

### 使用 Mono 官方源
```
$ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF
```
出现错误
```
Executing: /tmp/apt-key-gpghome.ZqIcif1Acm/gpg.1.sh --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF
gpg: failed to start the dirmngr '/usr/bin/dirmngr': No such file or directory
gpg: connecting dirmngr at '/tmp/apt-key-gpghome.ZqIcif1Acm/S.dirmngr' failed: No such file or directory
gpg: keyserver receive failed: No dirmngr
```
根据关键信息 `gpg: keyserver receive failed: No dirmngr`
```
$ sudo apt-cache search -n dirmngr
```
得到
```
dirmngr - GNU privacy guard - network certificate management service
```
安装软件
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

#### 完整安装
```
$ sudo apt-get install mono-complete
```
