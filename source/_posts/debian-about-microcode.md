---
title: Debian 关于微代码
date: 2017-07-19 23:22:53
categories: 技术
tags:
  - Debian
---

* 参考 huataihuang 的 [microcode](https://huataihuang.gitbooks.io/cloud-atlas/content/os/linux/kernel/cpu/microcode.html)
* 参考 [Should I install the AMD/Intel microcode packages when running Debian?](https://unix.stackexchange.com/questions/181646/should-i-install-the-amd-intel-microcode-packages-when-running-debian)
<!--more-->

> The microcode packages are basically patches to the CPU, fixing problems that have been detected since the CPU was manufactured. You only need to install the package related to the brand of CPU in the system (i.e. installing amd64-microcode on a system with an Intel processor makes no sense. In any case it can't hurt to install the appropriate microcode package, it may help to fix bugs that you might or might not encounter. See e.g. the Intel ucode page as per don_crissti's comment; AMD doesn't give detailed info AFAIK.
>
> They are in non-free as there are no freely available sources for the microcode; the packages are built on the binary blobs provided by Intel and AMD. As Debian is committed to providing a totally open and free operating system, these microcode updates don't really belong in Debian but they are provided as a service to Debian users. Those users who do not want to run any risk of running into licensing problems because of software which is not free (as in terms and conditions of use) can simply not use the non-free archive and so be sure to use only unrestricted software.

### 安装
Intel
```
$ sudo apt install intel-microcode
```

AMD
```
$ sudo apt install amd64-microcode
```
