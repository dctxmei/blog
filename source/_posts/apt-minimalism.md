---
title: Apt 极简主义
date: 2017-07-03 12:38:51
categories: 技术
tags:
  - Debian
---

* 参考 [Configure minimalism](http://wiki.friendsofdevuan.org/doku.php/quick_start_improved#configure_minimalism)
* 参考 Aptitude 的 [Available configuration options](https://aptitude.alioth.debian.org/doc/en/ch02s05s05.html)

### 警告
**这具有极强的杀伤力！**
**这可能会摧毁尼的现存环境！**
**请谨慎操作！**
<!--more-->

### 极简
```
$ sudo vi /etc/apt/apt.conf.d/01lean
```
添加
```
# 自动删除重要推荐
APT::AutoRemove::RecommendsImportant "false";
# 取消安装推荐
APT::Install-Recommends "false";
```

#### 清理依赖
```
$ sudo apt autoremove --purge
```
