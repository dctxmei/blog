---
title: 整理 R-18 漫畫
date: 2018-04-06 15:43:26
tags:
  - Linux
  - R-18
categories: 技術
---

## 在 Linux 欣賞 R-18 漫畫的正確操作

無論何種方式取得何種 R-18 漫畫。

一般，R-18 漫畫，在解壓後不會有目錄，先創建一個。

解壓（具體情況具體分析）。

視情況自行決定是否刪除原壓縮文件。

## 檢查圖片格式

推薦 JPG，若發現 PNG，可進行轉換

需安裝 Imagemagick。

將當前目錄所有 PNG 轉換為 JPG 並刪除源文件：

```
$ mogrify -format jpg *.png && rm *.png
```

## 檢查圖片名

個人推薦百張圖片以內為 00.jpg 01.jpg……（千張圖片則將「i=100」改為「i=1000」）：

```
$ i=100; for img in *.jpg; do mv $img ${i#1}.jpg; (( i ++ )); done
```

若要排除某圖片（「grep -v」後，「?」為需要排除的圖片名）：

```
$ i=100; for img in $(ls *.jpg | grep -v ?.jpg); do mv $img ${i#1}.jpg; (( i ++ )); done
```

## 壓縮

這裡用了一個小腳本：

```
#!/bin/bash

tar -cvf - $@ | xz -T0 -9 > $@.tar.xz
```
