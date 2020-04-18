---
title: 安装 V2Ray 到 Debian 的一次记录
date: 2018-04-07 12:39:18
categories: 技术
tags:
  - V2Ray
  - Debian
---

因为一台 nl vps 很闲，所以······
这只是记录而已
<!--more-->

### 换源
```
$ apt edit-sources
deb http://mirror.neostrada.nl/debian/ stretch main contrib non-free
deb-src http://mirror.neostrada.nl/debian/ stretch main contrib non-free
$ apt update
```
```
$ apt install apt-transport-https
$ apt edit-sources
deb https://mirror.neostrada.nl/debian/ stretch main contrib non-free
deb-src https://mirror.neostrada.nl/debian/ stretch main contrib non-free
```

跨版本升级
```
# apt update && DEBIAN_FRONTEND=noninteractive apt -o Dpkg::Options::="--force-confnew" full-upgrade
```

当前版本升级
```
# apt update && DEBIAN_FRONTEND=noninteractive apt -o Dpkg::Options::="--force-confold" -o Dpkg::Options::="--force-confdef" full-upgrade
```

### 安装 Zsh
```
# apt install sudo zsh
# useradd -m -s /bin/zsh mei
# usermod -a -G sudo mei
# vi /etc/zsh/zprofile
emulate sh -c 'source /etc/profile'
```

### 开启 bbr
```
$ echo net.core.default_qdisc=fq | sudo tee /etc/sysctl.d/bbr.conf
$ echo net.ipv4.tcp_congestion_control=bbr | sudo tee -a /etc/sysctl.d/bbr.conf
$ sudo sysctl --system
```

### WebSocket+TLS+Web

#### 安装 V2ray
```
wget -qO- https://install.direct/go.sh | sudo bash
```
具体参考 [V2ray 官网文档](https://v2ray.com/)

#### 安装 caddy
```
$ wget -qO- https://getcaddy.com | sed 's/\/local//' | sudo bash -s personal
```
```
$ sudo mkdir /etc/caddy
$ sudo vi /etc/caddy/Caddyfile
$ sudo mkdir /etc/ssl/caddy
```

### 服务文件
/usr/lib/systemd/system/caddy.service
```
[Unit]
Description=Caddy HTTP/2 web server
After=network.target

[Service]
Type=simple
Environment=CADDYPATH=/etc/ssl/caddy
ExecStart=/usr/bin/caddy -conf /etc/caddy/Caddyfile
KillSignal=SIGINT
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
```
```
$ sudo systemctl enable caddy.service
$ sudo systemctl start caddy.service
```
