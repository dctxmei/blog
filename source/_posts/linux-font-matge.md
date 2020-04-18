---
title: 在 Linux 下字体合成
date: 2017-07-03 17:28:01
categories: 技术
tags:
  - Linux
---

* 参考 [Merging Font with FontForge](http://7thgen.info/blog/2008/07/merging-font-with-fontforge/)
* 参考 [fontforge script to merge and replace font glyphs](https://cat-in-136.github.io/2010/02/fontforge-script-to-merge-and-replace.html)
* 参考 [[注解] [506] 华丽的 & 符号](https://github.com/cssmagic/CSS-Secrets/issues/63)
<!--more-->

### 准备
```
$ sudo apt install fontforge
```
<!--more-->

### 合成 Monaco and Powerline 实例
```
$ mkdir font-metge
$ cd font-metge
$ vi font-merge.pe
```
添加
```
# A font                                             
Open("Monaco_Linux.ttf")
SelectAll()
ScaleToEm(1024)
Generate("Temporary-A.ttf", "", 0x14)
Close()

# B font
Open("PowerlineSymbols.otf")
SelectAll()
ScaleToEm(1024)
Generate("Temporary-B.otf", "", 0x14)
Close()

# Merge font
Open("Temporary-A.ttf")
MergeFonts("Temporary-B.otf")
SetFontNames("Monaco_for_Powerline", "Monaco for Powerline", "Monaco", "Regolare", "")
Generate("Monaco for Powerline.otf", "", 0x14)
Close()
```

#### 下载所需字体
```
$ wget http://www.gringod.com/wp-upload/software/Fonts/Monaco_Linux.ttf
$ wget https://github.com/powerline/powerline/raw/develop/font/PowerlineSymbols.otf
```

#### 进行合成
```
$ fontforge -script font-merge.pe
```

---

### 说明
Monaco_Linux.ttf 为 A 字体
PowerlineSymbols.otf 为 B 字体
Monaco for Powerline.otf 为合成字体

`SetFontNames("Monaco_for_Powerline", "Monaco for Powerline", "Monaco", "Regolare", "")`
Monaco_for_Powerline 为 PostScript 名称
Monaco for Powerline 为全名
Monaco 为家族名
Regolare 为样式
