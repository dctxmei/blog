---
title: Debian 安裝 CUDA
date: 2017-07-19 22:34:25
tags:
  - Debian
  - NVIDIA
  - CUDA
categories: 技術
---

**本文內容已過時！**

* 參考 [NvidiaGraphicsDrivers](https://wiki.debian.org/NvidiaGraphicsDrivers#CUDA)
* 參考 [How to install CUDA Toolkit 7.x or 8 on Debian 8 (Jessie) or 9 (Stretch)?](https://unix.stackexchange.com/questions/218163/how-to-install-cuda-toolkit-7-x-or-8-on-debian-8-jessie-or-9-stretch)
* 參考 [Dive Into TensorFlow, Part III: GTX 1080+Ubuntu16.04+CUDA8.0+cuDNN5.0+TensorFlow](http://textminingonline.com/dive-into-tensorflow-part-iii-gtx-1080-ubuntu16-04-cuda8-0-cudnn5-0-tensorflow)
* 參考 [CUDA 8.0 missing required libraries on Ubuntu 16.04](https://devtalk.nvidia.com/default/topic/993862/cuda-8-0-missing-required-libraries-on-ubuntu-16-04/)
* 參考 [NVIDIA CUDA Installation Guide for Linux](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/#abstract)

### 安裝 NVIDIA 顯卡驅動

#### 單顯

```
$ sudo apt install nvidia-driver
```

#### 雙顯

```
$ sudo apt install bumblebee-nvidia nvidia-driver-libs nvidia-settings nvidia-smi primus primus-libs:i386
```

### 安裝 CUDA

前往 [CUDA Toolkit Download](https://developer.nvidia.com/cuda-downloads) 下載 Ubuntu 16.04 的 .run 文件

```
$ chmod +x cuda_*_linux-run
$ sudo ./cuda_*_linux-run --override
```
只安裝 CUDA 開發工具及樣本

#### 解決錯誤

```
Missing recommended library: libGLU.so
Missing recommended library: libXi.so
Missing recommended library: libXmu.so
Missing recommended library: libGL.so
```

```
$ sudo apt install libglu1-mesa-dev libxi-dev libxmu-dev
```

### 編譯 CUDA 樣本

```
$ export PATH=/usr/local/cuda-8.0/bin${PATH:+:${PATH}}
```

64-bit

```
$ export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64 \
                         ${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```

32-bit

```
$ export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib \
                         ${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```

```
$ export HOST_COMPILER=clang++-3.8
$ make
```
