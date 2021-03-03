---
title: hashcat 調用 GPU 加速跑字典
date: 2017-06-06 20:06:49
tags:
  - Linux
  - GPU
  - NVIDIA
categories: 技術
---

* 如何使用 Aircrack-ng 工具不在本文範圍內，請左轉谷歌之。
* 下文中已抓包至 ~/cap/-01.cap。
* 下文中「crunch.txt」爲「$ crunch 8 8 123456789 -o ~/crunch.txt」所生成。

#### 安裝 hashcat
```
$ git clone git@github.com:hashcat/hashcat.git && cd hashcat
$ git submodule update --init
$ make && sudo make install
```

#### 安裝調用 NVIDIA 驅動依賴
```
$ sudo apt-get install nvidia-libopencl1
```

#### 安裝 hashcat-utils
```
$ git clone git@github.com:hashcat/hashcat-utils.git && cd hashcat-utils/src
$ make && mv *.bin ../bin && cd ../bin
$ sudo install cap2hccapx.bin /usr/local/bin/cap2hccapx
```

#### 運行
```
$ cap2hccapx ~/cap/-01.cap ~/cap/-01.hccapx
$ optirun hashcat -m 2500 ~/cap/-01.hccapx crunch.txt
```
