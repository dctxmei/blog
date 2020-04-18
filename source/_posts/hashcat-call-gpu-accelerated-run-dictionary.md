---
title: hashcat 调用 GPU 加速跑字典
date: 2017-06-06 20:06:49
categories: 技术
tags:
  - Linux
  - GPU
  - NVIDIA
---

* 如何使用 Aircrack-ng 工具不在本文范围内，请左转谷歌之。
* 下文中已抓包至 ~/cap/-01.cap。
* 下文中“crunch.txt”为“$ crunch 8 8 123456789 -o ~/crunch.txt” 所生成。
* 本文的相关讨论可前往 debian吧 的 [记录一次 hashcat GPU 加速跑字典](http://tieba.baidu.com/p/5106027162)
<!--more-->

#### 安装 hashcat
```
$ git clone git@github.com:hashcat/hashcat.git && cd hashcat
$ git submodule update --init
$ make && sudo make install
```

#### 安装调用 NVIDIA 驱动依赖
```
$ sudo apt-get install nvidia-libopencl1
```

#### 安装 hashcat-utils
```
$ git clone git@github.com:hashcat/hashcat-utils.git && cd hashcat-utils/src
$ make && mv *.bin ../bin && cd ../bin
$ sudo install cap2hccapx.bin /usr/local/bin/cap2hccapx
```

#### 运行
```
$ cap2hccapx ~/cap/-01.cap ~/cap/-01.hccapx
$ optirun hashcat -m 2500 ~/cap/-01.hccapx crunch.txt
```
