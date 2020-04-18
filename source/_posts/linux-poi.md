---
title: Linux 运行 poi
date: 2017-07-10 20:37:04
categories: 技术
tags:
  - Linux
---

* 参考 poi 的 [Run](https://github.com/poooi/poi#run)
* 参考 electron-download 的 [Usage](https://github.com/electron-userland/electron-download#usage)
<!--more-->

### Go
```
$ git clone git@github.com:poooi/poi.git && cd poi
```
```
$ cat << EOF > ~/.npmrc
electron_mirror="https://npm.taobao.org/mirrors/electron/"
EOF
```
```
$ npm install
$ npm run deploy
$ npm start
```

### 解决错误
```
error while loading shared libraries: libgconf-2.so.4: cannot open shared object file: No such file or directory
```
```
$ sudo apt install libgconf-2-4
```
