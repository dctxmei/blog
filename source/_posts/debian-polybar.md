---
title: Debian 安装 Polybar
date: 2017-08-23 23:23:22
categories: 技术
tags:
  - Debian
---

* 参考 [Compiling](https://github.com/jaagr/polybar/wiki/Compiling)

### 依赖
```
$ sudo apt install build-essential clang cmake cmake-data i3-wm libasound2-dev libcairo2-dev libcurl4-openssl-dev libiw-dev libmpdclient-dev libxcb1-dev libxcb-ewmh-dev libxcb-icccm4-dev libxcb-image0-dev libxcb-randr0-dev libxcb-util0-dev libxcb-xkb-dev libxcb-xrm-dev pkg-config python-xcbgen xcb-proto
```
<!--more-->

### 编译
```
$ git clone git@github.com:jaagr/polybar.git --recursive
$ cd polybar
$ mkdir build && cd build
$ cmake -DCMAKE_C_COMPILER=clang -DCMAKE_CXX_COMPILER=clang++ ..
$ sudo make install
```
