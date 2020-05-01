---
title: Apt 極簡主義
date: 2017-07-03 12:38:51
tags:
  - Debian
categories: 技術
---

**本文內容已過時！**

* 參考 [Configure minimalism](http://wiki.friendsofdevuan.org/doku.php/quick_start_improved#configure_minimalism)
* 參考 Aptitude 的 [Available configuration options](https://aptitude.alioth.debian.org/doc/en/ch02s05s05.html)

### 警告

**這具有極強的殺傷力！**
**這可能會摧毀你的現存環境！**
**請謹慎操作！**

### 極簡

```
$ sudo vi /etc/apt/apt.conf.d/01lean
```
添加
```
# 自動刪除重要推薦
APT::AutoRemove::RecommendsImportant "false";
# 取消安裝推薦
APT::Install-Recommends "false";
```

#### 清理依賴

```
$ sudo apt autoremove --purge
```
