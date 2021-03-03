---
title: 嘗試 Intel GVT-g
date: 2020-12-24 19:44:38
tags:
  - Intel GVT-g
  - libvirt
categories: 技術
toc: true
---

## 前期準備

1. 添加內核參數 `i915.enable_gvt=1`。

    如果是 Grub，那就添加到 `/etc/default/grub` 的 `GRUB_CMDLINE_LINUX=` 當中，然後執行一下：

    ```
    # grub-mkconfig
    ```

    如果是 systemd-boot，那就是：

    ```
    options i915.enable_gvt=1
    ```

    然後執行一下：

    ```
    # bootctl update
    ```

2. 將 `kvmgt`、`vfio-iommu-type1` 和 `vfio-mdev` 添加到 `/etc/mkinitcpio.conf` 的 `MODULES=()` 當中，然後執行 `mkinitcpio -P` 重新生成 initramfs。

## libvirt

### 掛個 hook

將以下內容[<sup>1</sup>](#intel_gvt-g_libvirt_qemu_hook)寫入 `/etc/libvirt/hooks/qemu`：

```bash
#!/bin/bash

GVT_PCI=<GVT_PCI>
GVT_GUID=<GVT_GUID>
MDEV_TYPE=<GVT_TYPE>
DOMAIN=<DOMAIN name>
if [ $# -ge 3 ]; then
    if [ $1 = "$DOMAIN" -a $2 = "prepare" -a $3 = "begin" ]; then
        echo "$GVT_GUID" > "/sys/bus/pci/devices/$GVT_PCI/mdev_supported_types/$MDEV_TYPE/create"
    elif [ $1 = "$DOMAIN" -a $2 = "release" -a $3 = "end" ]; then
        echo 1 > /sys/bus/pci/devices/$GVT_PCI/$GVT_GUID/remove
    fi
fi
```

記得添加可執行權限：

```
# chmod +x /etc/libvirt/hooks/qemu
```

### 修改虛擬機配置檔案

```
# virsh edit <DOMAIN name>
```

檔案首行修改爲：

```xml
<domain type='kvm' xmlns:qemu='http://libvirt.org/schemas/domain/qemu/1.0'>
```
在 `</devices>` 前添加：

```xml
    <hostdev mode='subsystem' type='mdev' managed='no' model='vfio-pci' display='off'>
      <source>
        <address uuid='a6bc35ff-42e8-48bd-ba5c-0e3b397fd326'/>
      </source>
    </hostdev>
```

此處的 UUID 對應上文中的 GVT_GUID。

然後啓動虛擬機，檢查虛擬機是否有監測到新的顯示設備（如果是 Windows，應同時檢查是否有安裝相應驅動）。

### 僅使用 Intel GVT-g

將上文中的 `display='off'` 修改爲 `display='on'`。

刪除原有的 `<graphics>...</graphics>` 和 `<video>...</video>`，用以下內容代替：

```xml
    <graphics type='spice'>
      <listen type='none'/>
      <gl enable='yes' rendernode='/dev/dri/by-path/pci-0000:00:02.0-render'/>
    </graphics>
```

```xml
    <video>
      <model type='none'/>
    </video>
```

此處的 PCI 對應上文中的 GVT_PCI。

在 `</domain>` 前添加[<sup>2</sup>](#configure_intel_gvt-g_virtual_core_display)：

```xml
  <qemu:commandline>
    <qemu:arg value='-set'/>
    <qemu:arg value='device.hostdev0.ramfb=on'/>
    <qemu:arg value='-set'/>
    <qemu:arg value='device.hostdev0.driver=vfio-pci-nohotplug'/>
    <qemu:arg value='-set'/>
    <qemu:arg value='device.hostdev0.x-igd-opregion=on'/>
    <qemu:arg value='-set'/>
    <qemu:arg value='device.hostdev0.romfile=/vbios_gvt_uefi.rom'/>
    <qemu:env name='MESA_LOADER_DRIVER_OVERRIDE' value='i965'/>
  </qemu:commandline>
```

其中的 `vbios_gvt_uefi.rom` 僅在虛擬機爲 UEFI 環境的情況下使用，且需要前往

http://120.25.59.132:3000/vbios_gvt_uefi.rom

下載並放到指定路徑。

## 參考

<div id="intel_gvt-g_libvirt_qemu_hook"></div>

* [1]: [Intel_GVT-g - ArchWiki](https://wiki.archlinux.org/index.php/Intel_GVT-g#libvirt_qemu_hook)

<div id="configure_intel_gvt-g_virtual_core_display"></div>

* [2]: [筆記本 Optimus MUXless 下的 Intel 和 NVIDIA 虛擬機顯卡直通 | Lan Tian @ Blog](https://lantian.pub/article/modify-computer/laptop-intel-nvidia-optimus-passthrough.lantian/#%E9%85%8D%E7%BD%AE-Intel-GVT-g-%E8%99%9A%E6%8B%9F%E6%A0%B8%E6%98%BE)
