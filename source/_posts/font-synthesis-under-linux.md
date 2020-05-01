---
title: Linux 下的字體合成
date: 2017-07-03 17:28:01
tags:
  - Linux
  - 字體
categories: 技術
---

* 參考 [Merging Font with FontForge](http://7thgen.info/blog/2008/07/merging-font-with-fontforge/)
* 參考 [fontforge script to merge and replace font glyphs](https://cat-in-136.github.io/2010/02/fontforge-script-to-merge-and-replace.html)
* 參考 [[註解] [506] 華麗的 & 符號](https://github.com/cssmagic/CSS-Secrets/issues/63)

# 準備

```
# apt install fontforge
```

## 合成 Monaco and Powerline 實例

```
$ mkdir font-metge
$ cd font-metge
```

```
$ vi font-merge.pe

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

### 下載所需字體

```
$ wget http://www.gringod.com/wp-upload/software/Fonts/Monaco_Linux.ttf
$ wget https://github.com/powerline/powerline/raw/develop/font/PowerlineSymbols.otf
```

### 進行合成

```
$ fontforge -script font-merge.pe
```

## 說明

Monaco_Linux.ttf 為 A 字體
PowerlineSymbols.otf 為 B 字體
Monaco for Powerline.otf 為合成字體

```
SetFontNames("Monaco_for_Powerline", "Monaco for Powerline", "Monaco", "Regolare", "")
```

Monaco_for_Powerline 為 PostScript 名稱
Monaco for Powerline 為全名
Monaco 為家族名
Regolare 為樣式
