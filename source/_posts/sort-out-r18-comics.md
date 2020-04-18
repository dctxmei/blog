---
title: 整理「本子」
date: 2018-04-06 15:43:26
categories: 技术
tags:
  - Linux
  - Hentai
---

「本子」乃是人类文明之精华，广阔宇宙之希望，是······对不起我编不下去了！
<!--more-->

## 在 linux 欣赏「本子」的正确操作
无论何种方式取得何种「本子」

一般「本子」解压后不会有目录，先创建一个排除后缀名的相同目录
![](https://ws1.sinaimg.cn/large/0076LFY4gy1fq2ynu1d45j310s07ojza.jpg)

解压（具体情况具体分析）
![](https://ws1.sinaimg.cn/large/0076LFY4ly1fq2yr0bpkrj310s0gzk74.jpg)

视情况自行决定是否删除原压缩文件

### 检查图片格式
推荐 jpg，若发现 png，可进行转换

需安装 imagemagick

将当前目录所有 png 转换为 jpg 并删除源文件
```
$ mogrify -format jpg *.png && rm *.png
```

### 检查图片名
个人推荐百张图片以内为 00.jpg 01.jpg······（千张图片则将“i=100”改为“i=1000”）
```
$ i=100; for img in *.jpg; do mv $img ${i#1}.jpg; (( i ++ )); done
```

若要排除某图片（“grep -v”后，“?”为需要排除的图片名）
```
$ i=100; for img in $(ls *.jpg | grep -v ?.jpg); do mv $img ${i#1}.jpg; (( i ++ )); done
```

![](https://ws1.sinaimg.cn/large/0076LFY4ly1fq2yxtx4chj310s03pgpd.jpg)

### 压缩
这里用了一个小脚本
```
#!/bin/zsh

tar -cvf - $@ | xz -T0 -9 > $@.tar.xz
```

![最终结果](https://ws1.sinaimg.cn/large/0076LFY4gy1fq30a3rc6oj311y0lc1kz.jpg)
