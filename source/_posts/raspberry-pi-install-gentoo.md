---
title: Raspberry Pi 安装 Gentoo
date: 2017-07-19 23:35:59
categories: 技术
tags:
  - Raspberry Pi
  - Gentoo
toc: true
---

* 参考一只可爱的男孩子的 [在Raspberry Pi 2上安装Gentoo的故事？](https://www.futures.moe/writings/install-gentoo-on-raspberry-pi-2.htm)
* 参考 [Raspberry Pi/Quick Install Guide](https://wiki.gentoo.org/wiki/Raspberry_Pi/Quick_Install_Guide)
<!--more-->

### 准备
* 一张 Raspberry Pi 3
* 一张 Micro SD Card 和读卡器
* 任意 Linux OS（本文档以 Debian 为标准）
* 流畅的网络

### 分区
```
$ sudo fdisk /dev/sdb
```

armhf
```
$ sudo mkfs.vfat -F 16 /dev/sdb1
$ sudo mkfs.ext4 /dev/sdb2
$ sudo mkswap /dev/sdb3
```
arm64
```
$ sudo mkfs.vfat -F 32 /dev/sdb1
$ sudo mkfs.ext4 -i 8192 /dev/sdb2
$ sudo mkswap /dev/sdb3
```

#### 挂载
```
$ sudo mkdir /mnt/gentoo
$ sudo mount /dev/sdb2 /mnt/gentoo
$ sudo mkdir /mnt/gentoo/boot
$ sudo mount /dev/sdb1 /mnt/gentoo/boot
```

#### 卸载
```
$ sudo umount /mnt/gentoo/boot
$ sudo umount /mnt/gentoo
```

---

### 安装
#### Stage 3
armhf
前往 [Gentoo](http://distfiles.gentoo.org/experimental/arm/hardened/) 下载 `ARMv7a | HardFP`
```
$ sudo tar -jxpvf stage3-armv7a_hardfp-*.tar.bz2 -C /mnt/gentoo
```
arm64
前往 [Gentoo](http://distfiles.gentoo.org/experimental/arm64/) 下载 `ARM64`
```
$ sudo tar -jxpvf stage3-arm64-*.tar.bz2 -C /mnt/gentoo
```

#### Portage tree
```
$ wget http://distfiles.gentoo.org/snapshots/portage-latest.tar.xz
$ sudo tar -Jxvf portage-latest.tar.xz -C /mnt/gentoo/usr
```

#### 固件
```
$ git clone --depth 1 git@github.com:raspberrypi/firmware.git
$ sudo chown -R 0:0 firmware
$ cd firmware/boot
$ sudo cp -r * /mnt/gentoo/boot
$ sudo cp -r ../modules /mnt/gentoo/lib
```

#### 内核
armhf
```
$ sudo apt install gcc-arm-linux-gnueabihf libc6-dev-armhf-cross
$ git clone --depth 1 git@github.com:raspberrypi/linux.git
$ cd linux
$ make ARCH=arm bcm2709_defconfig
$ make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- oldconfig
$ make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- -j$(nproc)
$ sudo make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- modules_install INSTALL_MOD_PATH=/mnt/gentoo
$ sudo cp arch/arm/boot/zImage /mnt/gentoo/boot/kernel7.img
$ sudo cp arch/arm/boot/dts/*.dtb /mnt/gentoo/boot
$ sudo cp arch/arm/boot/dts/overlays/* /mnt/gentoo/boot/overlays
```
arm64
```
$ sudo apt install gcc-aarch64-linux-gnu libc6-dev-arm64-cross
$ git clone --depth 1 git@github.com:raspberrypi/linux.git
$ cd linux
$ make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- bcmrpi3_defconfig
$ make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- -j$(nproc)
$ sudo make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- modules_install INSTALL_MOD_PATH=/mnt/gentoo
$ sudo cp arch/arm64/boot/Image /mnt/gentoo/boot/kernel8.img
$ sudo mv /mnt/gentoo/boot/bcm2710-rpi-3-b.dtb /mnt/gentoo/boot/bcm2710-rpi-3-b.dtb_32
$ sudo cp arch/arm64/boot/dts/broadcom/bcm2710-rpi-3-b.dtb /mnt/gentoo/boot
```

#### WiFi
```
$ git clone --depth 1 git@github.com:RPi-Distro/firmware-nonfree.git
$ sudo chown -R 0:0 firmware-nonfree
$ sudo mkdir /mnt/gentoo/lib/firmware/brcm
$ sudo cp firmware-nonfree/brcm80211/brcm/brcmfmac43430-sdio.* /mnt/gentoo/lib/firmware/brcm
```

#### Bluetooth
```
$ git clone --depth 1 git@github.com:OpenELEC/misc-firmware.git
$ sudo chown -R 0:0 misc-firmware
$ sudo mkdir /mnt/gentoo/etc/firmware
$ sudo cp misc-firmware/firmware/brcm/BCM43430A1.hcd /etc/firmware
```
```
$ sudo vi /mnt/gentoo/etc/local.d/bluetooth.start
```
```
#!/bin/bash
# Bluetooth is attached to /dev/ttyAMA0 so attach it and 
# load the firmware 
/usr/bin/hciattach /dev/ttyAMA0 bcm43xx 921600 noflow -
```

---

### Chroot
#### 进入
```
$ sudo apt install binfmt-support qemu-user-static
$ sudo cp /usr/bin/qemu-arm-static /mnt/gentoo/usr/bin
```
```
$ sudo mount -o bind /dev /mnt/gentoo/dev
$ sudo mount -o bind /proc /mnt/gentoo/proc
$ sudo mount -o bind /sys /mnt/gentoo/sys
$ sudo mount -o rw,nosuid,noexec,relatime,gid=5,mode=620,ptmxmode=000 devpts /mnt/gentoo/dev/pts -t devpts
$ sudo chroot /mnt/gentoo /bin/bash
```

#### 退出
```
$ sudo rm /mnt/gentoo/usr/bin/qemu-arm-static
$ sudo umount /mnt/gentoo/dev/pts
$ sudo umount /mnt/gentoo/sys
$ sudo umount /mnt/gentoo/proc
$ sudo umount /mnt/gentoo/dev
```

---

### 调试
#### 设定密码
```
# passwd
```

#### 分区挂载表
```
# nano /etc/fstab
```
```
/dev/mmcblk0p1		/boot		vfat		noauto,noatime	1 2
/dev/mmcblk0p2		/   		ext4		noatime	    	0 1
/dev/mmcblk0p3		none		swap		sw  	        0 0
```

#### Config
```
# nano /boot/config.txt
```
```
# have a properly sized image
disable_overscan=1

# lets have the VC4 hardware accelerated video
dtoverlay=vc4-fkms-v3d

# for type over HDMI
hdmi_group=2
hdmi_mode=9

# for sound over HDMI
hdmi_drive=2

# Enable audio (loads snd_bcm2835)
dtparam=audio=on

# gpu_mem is for closed-source driver only; since we are only using the
# open-source driver here, set low
gpu_mem=16

# Overclocking
arm_freq=950
core_freq=450
sdram_freq=450
over_voltage=6
force_turbo=1
```

#### 设置启动选项
```
# nano /boot/cmdline.txt
```
```
dwc_otg.lpm_enable=0 console=ttyAMA0,115200 kgdboc=ttyAMA0,115200 console=tty1 root=/dev/mmcblk0p2 rootfstype=ext4 elevator=deadline rootwait
```

#### 启动时启用 eth0 网络
```
# cd /etc/init.d
# ln -sv net.lo net.eth0
# rc-service start net.eth0
# rc-update add net.eth0 boot
```
```
# rc-update --update
```

#### 自启 sshd
```
# rc-update add sshd default
```

#### 设置 make.conf
```
# nano /etc/portage/make.conf
```
```
CFLAGS="-O2 -march=armv7-a -mfpu=neon-vfpv4 -mfloat-abi=hard"
CXXFLAGS="${CFLAGS}"
```

#### 设置时区
```
# ln -fs /mnt/gentoo/usr/share/zoneinfo/Asia/Shanghai /mnt/gentoo/etc/localtime
```

#### 停止警告
```
# nano /etc/rc.conf
```
```
rc_sys=""
```
```
# nano /etc/inittab
```
```
# SERIAL CONSOLES
#s0:12345:respawn:/sbin/agetty 9600 ttyS0 vt100
#s1:12345:respawn:/sbin/agetty 9600 ttyS1 vt100
```
