---
title: Raspberry Pi 安裝 Gentoo
date: 2017-07-19 23:35:59
tags:
  - Raspberry Pi
  - Gentoo
categories: 技術
toc: true
---

* 參考一隻可愛的男孩子的 [在Raspberry Pi 2上安裝Gentoo的故事？](https://www.futures.moe/writings/install-gentoo-on-raspberry-pi-2.htm)
* 參考 [Raspberry Pi/Quick Install Guide](https://wiki.gentoo.org/wiki/Raspberry_Pi/Quick_Install_Guide)

## 準備

* 一張 Raspberry Pi 3
* 一張 Micro SD Card 和讀卡器
* 任意 Linux OS（本文檔以 Debian 為標準）
* 流暢的網絡

## 分區

```
# fdisk /dev/sdb
```

### armhf

```
# mkfs.vfat -F 16 /dev/sdb1
# mkfs.ext4 /dev/sdb2
# mkswap /dev/sdb3
```

### arm64

```
# mkfs.vfat -F 32 /dev/sdb1
# mkfs.ext4 -i 8192 /dev/sdb2
# mkswap /dev/sdb3
```

## 掛載

```
# mkdir /mnt/gentoo/
# mount /dev/sdb2 /mnt/gentoo/
# mkdir /mnt/gentoo/boot/
# mount /dev/sdb1 /mnt/gentoo/boot/
```

## 卸載

```
# umount /mnt/gentoo/boot/
# umount /mnt/gentoo/
```

## 安裝

### Stage 3

#### armhf

前往 [Gentoo](http://distfiles.gentoo.org/experimental/arm/hardened/) 下載 `ARMv7a | HardFP`

```
# tar -jxpvf stage3-armv7a_hardfp-*.tar.bz2 -C /mnt/gentoo/
```

#### arm64

前往 [Gentoo](http://distfiles.gentoo.org/experimental/arm64/) 下載 `ARM64`

```
# tar -jxpvf stage3-arm64-*.tar.bz2 -C /mnt/gentoo
```

### Portage tree

```
$ wget http://distfiles.gentoo.org/snapshots/portage-latest.tar.xz
# tar -Jxvf portage-latest.tar.xz -C /mnt/gentoo/usr/
```

### 固件

```
$ git clone --depth 1 git@github.com:raspberrypi/firmware.git
# chown -R 0:0 firmware/
# cd firmware/boot/
# cp -r * /mnt/gentoo/boot/
# cp -r ../modules/ /mnt/gentoo/lib/
```

### 內核

#### armhf

```
# apt install gcc-arm-linux-gnueabihf libc6-dev-armhf-cross
$ git clone --depth 1 git@github.com:raspberrypi/linux.git
$ cd linux/
$ make ARCH=arm bcm2709_defconfig
$ make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- oldconfig
$ make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- -j$(nproc)
# make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- modules_install INSTALL_MOD_PATH=/mnt/gentoo/
# cp arch/arm/boot/zImage /mnt/gentoo/boot/kernel7.img
# cp arch/arm/boot/dts/*.dtb /mnt/gentoo/boot/
# cp arch/arm/boot/dts/overlays/* /mnt/gentoo/boot/overlays/
```

#### arm64

```
# apt install gcc-aarch64-linux-gnu libc6-dev-arm64-cross
$ git clone --depth 1 git@github.com:raspberrypi/linux.git
$ cd linux/
$ make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- bcmrpi3_defconfig
$ make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- -j$(nproc)
# make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- modules_install INSTALL_MOD_PATH=/mnt/gentoo/
# cp arch/arm64/boot/Image /mnt/gentoo/boot/kernel8.img
# mv /mnt/gentoo/boot/bcm2710-rpi-3-b.dtb /mnt/gentoo/boot/bcm2710-rpi-3-b.dtb_32
# cp arch/arm64/boot/dts/broadcom/bcm2710-rpi-3-b.dtb /mnt/gentoo/boot/
```

### WiFi

```
$ git clone --depth 1 git@github.com:RPi-Distro/firmware-nonfree.git
# chown -R 0:0 firmware-nonfree/
# mkdir /mnt/gentoo/lib/firmware/brcm/
# cp firmware-nonfree/brcm80211/brcm/brcmfmac43430-sdio.* /mnt/gentoo/lib/firmware/brcm/
```

#### Bluetooth

```
$ git clone --depth 1 git@github.com:OpenELEC/misc-firmware.git
# chown -R 0:0 misc-firmware/
# mkdir /mnt/gentoo/etc/firmware/
# cp misc-firmware/firmware/brcm/BCM43430A1.hcd /etc/firmware/
```

```
# vi /mnt/gentoo/etc/local.d/bluetooth.start

#!/bin/bash
# Bluetooth is attached to /dev/ttyAMA0 so attach it and 
# load the firmware 
/usr/bin/hciattach /dev/ttyAMA0 bcm43xx 921600 noflow -
```

## Chroot

### 進入

```
# apt install binfmt-support qemu-user-static
# cp /usr/bin/qemu-arm-static /mnt/gentoo/usr/bin/
```

```
# mount -o bind /dev/ /mnt/gentoo/dev/
# mount -o bind /proc/ /mnt/gentoo/proc/
# mount -o bind /sys/ /mnt/gentoo/sys/
# mount -o rw,nosuid,noexec,relatime,gid=5,mode=620,ptmxmode=000 devpts /mnt/gentoo/dev/pts/ -t devpts
# chroot /mnt/gentoo/ /bin/bash
```

### 退出

```
# rm /mnt/gentoo/usr/bin/qemu-arm-static
# umount /mnt/gentoo/dev/pts
# umount /mnt/gentoo/sys
# umount /mnt/gentoo/proc
# umount /mnt/gentoo/dev
```

## 調試

### 設定密碼

```
# passwd
```

### 分區掛載表

```
# nano /etc/fstab

/dev/mmcblk0p1		/boot		vfat		noauto,noatime	1 2
/dev/mmcblk0p2		/   		ext4		noatime	    	0 1
/dev/mmcblk0p3		none		swap		sw  	        0 0
```

### Config

```
# nano /boot/config.txt

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

### 設置啟動選項

```
# nano /boot/cmdline.txt

dwc_otg.lpm_enable=0 console=ttyAMA0,115200 kgdboc=ttyAMA0,115200 console=tty1 root=/dev/mmcblk0p2 rootfstype=ext4 elevator=deadline rootwait
```

### 啟動時啟用 eth0 網絡

```
# cd /etc/init.d/
# ln -sv net.lo net.eth0
# rc-service start net.eth0
# rc-update add net.eth0 boot
```

```
# rc-update --update
```

### 自啟 sshd

```
# rc-update add sshd default
```

### 設置 make.conf

```
# nano /etc/portage/make.conf
```

```
CFLAGS="-O2 -march=armv7-a -mfpu=neon-vfpv4 -mfloat-abi=hard"
CXXFLAGS="${CFLAGS}"
```

### 設置時區

```
# ln -fs /mnt/gentoo/usr/share/zoneinfo/Asia/Shanghai /mnt/gentoo/etc/localtime
```

### 停止警告

```
# nano /etc/rc.conf

rc_sys=""
```

```
# nano /etc/inittab

# SERIAL CONSOLES
#s0:12345:respawn:/sbin/agetty 9600 ttyS0 vt100
#s1:12345:respawn:/sbin/agetty 9600 ttyS1 vt100
```
