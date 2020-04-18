---
title: 我的 pip3
date: 2017-10-02 14:23:55
categories: 技术
tags:
  - Linux
  - Python
---

* 参考 [pip Installation](https://pip.pypa.io/en/stable/installing/)
<!--more-->

### 安装
```
$ aria2c https://bootstrap.pypa.io/get-pip.py
$ python3 get-pip.py --user
```

~/.zshrc
```
alias pipi="pip install --user"
```
~/.pip/pip.conf
```
[global]
index-url = https://mirrors.ustc.edu.cn/pypi/web/simple
format = columns
```

### 我目前所使用的软件
* speedtest-cli
* thefuck
* ykdl
* youtube-dl

```
$ pipi speedtest-cli thefuck ykdl youtube-dl
```

### 清理缓存
```
$ rm -r ~/.cache/pip
```

### 仅卸载 pip3
```
$ pip uninstall pip setuptools wheel
```
