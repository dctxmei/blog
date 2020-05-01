---
title: Debian VIM 配置
date: 2017-07-03 12:15:21
tags:
  - Debian
  - VIM
categories: 技術
toc: true
---

**本文內容已過時！**

* 參考 [yangyangwithgnu](https://github.com/yangyangwithgnu) 的 [use_vim_as_ide](https://github.com/yangyangwithgnu/use_vim_as_ide)

### 安裝
```
$ sudo apt autoremove --purge vim*
```
```
$ sudo apt install liblua5.3-dev libncurses5-dev libperl-dev python3-dev ruby-dev
$ git clone git@github.com:vim/vim.git
$ cd vim
$ ./configure \
    --enable-cscope \
    --enable-luainterp \
    --enable-multibyte \
    --enable-perlinterp \
    --enable-python3interp \
    --enable-rubyinterp \
    --with-features=huge \
    --with-python3-config-dir=/usr/lib/python3.6/config-3.6m-$(arch)-linux-gnu
$ make -j$(nproc)
$ sudo make install
```
```
$ sudo update-alternatives --install /usr/bin/editor editor /usr/local/bin/vim 0
$ sudo update-alternatives --set editor /usr/local/bin/vim
$ sudo update-alternatives --install /usr/bin/vi vi /usr/local/bin/vim 0
$ sudo update-alternatives --set vi /usr/local/bin/vim
```

### GUI 安裝
```
$ sudo apt autoremove --purge vim*
```
```
$ sudo apt install libgtk-3-dev liblua5.3-dev libncurses5-dev libperl-dev libx11-dev libxt-dev python3-dev ruby-dev
$ git clone git@github.com:vim/vim.git
$ cd vim
$ ./configure \
    --enable-cscope \
    --enable-gui=gtk3 \
    --enable-luainterp \
    --enable-multibyte \
    --enable-perlinterp \
    --enable-python3interp \
    --enable-rubyinterp \
    --with-features=huge \
    --with-python3-config-dir=/usr/lib/python3.6/config-3.6m-$(arch)-linux-gnu
$ make -j$(nproc)
$ sudo make install
```
```
$ sudo update-alternatives --install /usr/bin/editor editor /usr/local/bin/gvim 0
$ sudo update-alternatives --set editor /usr/local/bin/gvim
$ sudo update-alternatives --install /usr/bin/vi vi /usr/local/bin/gvim 0
$ sudo update-alternatives --set vi /usr/local/bin/gvim
```

### YCM
* stable - libclang-3.9-dev
* testing - libclang-5.0-dev
* unstable - libclang-6.0-dev

```
$ sudo apt install libclang-6.0-dev python3-dev
$ mkdir ycm_build && cd ycm_build
$ cmake -G "Unix Makefiles" -DUSE_PYTHON2=OFF -DUSE_SYSTEM_LIBCLANG=ON . \
~/.vim/bundle/YouCompleteMe/third_party/ycmd/cpp
$ cmake --build . --target ycm_core
```

### 解決錯誤
上述方案編譯後
```
$ vi --version | grep lua
```
會發現 `-lua`，原因如下（未來將會變動）
```
checking Lua version... (cached) 5.0.3
```
需
#### 鏈接
```
$ sudo update-alternatives --install /usr/include/lua5.0.3 lua5.0.3 /usr/include/lua5.3 0
$ sudo update-alternatives --install /usr/lib/liblua5.0.3.so liblua5.0.3 /usr/lib/x86_64-linux-gnu/liblua5.3.so 0
```
後重新編譯即可

---

#### 刪除
```
$ sudo update-alternatives --remove-all lua5.0.3
$ sudo update-alternatives --remove-all liblua5.0.3
```
