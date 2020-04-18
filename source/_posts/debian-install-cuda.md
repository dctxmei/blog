---
title: Debian 安装 CUDA
date: 2017-07-19 22:34:25
categories: 技术
tags:
  - Debian
  - NVIDIA
---

* 参考 [NvidiaGraphicsDrivers](https://wiki.debian.org/NvidiaGraphicsDrivers#CUDA)
* 参考 [How to install CUDA Toolkit 7.x or 8 on Debian 8 (Jessie) or 9 (Stretch)?](https://unix.stackexchange.com/questions/218163/how-to-install-cuda-toolkit-7-x-or-8-on-debian-8-jessie-or-9-stretch)
* 参考 [Dive Into TensorFlow, Part III: GTX 1080+Ubuntu16.04+CUDA8.0+cuDNN5.0+TensorFlow](http://textminingonline.com/dive-into-tensorflow-part-iii-gtx-1080-ubuntu16-04-cuda8-0-cudnn5-0-tensorflow)
* 参考 [CUDA 8.0 missing required libraries on Ubuntu 16.04](https://devtalk.nvidia.com/default/topic/993862/cuda-8-0-missing-required-libraries-on-ubuntu-16-04/)
* 参考 [NVIDIA CUDA Installation Guide for Linux](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/#abstract)
<!--more-->

### 安装 NVIDIA 显卡驱动
#### 单显
```
$ sudo apt install nvidia-driver
```

#### 双显
```
$ sudo apt install bumblebee-nvidia nvidia-driver-libs nvidia-settings nvidia-smi primus primus-libs:i386
```

### 安装 CUDA
前往 [CUDA Toolkit Download](https://developer.nvidia.com/cuda-downloads) 下载 Ubuntu 16.04 的 .run 文件
```
$ chmod +x cuda_*_linux-run
$ sudo ./cuda_*_linux-run --override
```
只安装 CUDA 开发工具及样本

#### 解决错误
```
Missing recommended library: libGLU.so
Missing recommended library: libXi.so
Missing recommended library: libXmu.so
Missing recommended library: libGL.so
```
```
$ sudo apt install libglu1-mesa-dev libxi-dev libxmu-dev
```

### 编译 CUDA 样本
```
$ export PATH=/usr/local/cuda-8.0/bin${PATH:+:${PATH}}
```
64-bit
```
$ export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64\
                         ${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```
32-bit
```
$ export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib\
                         ${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```
```
$ export HOST_COMPILER=clang++-3.8
$ make
```
