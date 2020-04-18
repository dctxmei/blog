---
title: 互联网审查规避
date: 2019-01-27 14:29:02
categories: 技术
tags:
  - GFW
  - Shadowsocks
  - V2Ray
  - Nginx
  - DoH
  - Firefox
  - Tor
toc: true
---

突破网络审查或突破网络封锁，俗称翻墙、穿墙、爬墙、科学上网、魔法上网、爱国上网、自由上网、正常上网等。由于“翻墙”在国内成为敏感词汇，现在更多的使用科学上网来代替“翻墙”，通常特指在中国大陆绕过互联网审查封锁技术（IP 封锁、端口封锁、关键词过滤、域名劫持等），突破防火长城，实现对网络内容的访问。
<!--more-->

突破网络审查的软件通常被称作翻墙软件，俗称梯子。翻墙软件并不只是 VPN、代理软件。它们着眼于获得被封锁的网站内容，并在访问受限网站时向 ISP 隐藏自己的真实地址信息。 

——维基百科 · [突破网络审查](https://zh.wikipedia.org/wiki/%E7%AA%81%E7%A0%B4%E7%BD%91%E7%BB%9C%E5%AE%A1%E6%9F%A5)

## 前言

在原则上，授人以鱼不如授人以渔，本文主要是对相关知识的归纳与演绎，同时避免重复造轮，本人保证一切内容皆经过本人实践，并且建议各位读者能够在舒适安逸的条件下阅读本文的同时查阅相关资料，以获得更好的阅读体验。

在实际上，Linux 对比 Windows 和 Mac OS，拥有更好的安全性与定制性，并且，由于本人是 Linux 重度用户，因此本文内容会不可避免的倾向于 Linux，本人推荐各位使用 Linux 并抛弃 Windows。
至于什么是 Linux？详见「维基百科·[Linux](https://zh.wikipedia.org/wiki/Linux)」。

本文的论点在于互联网审查规避，这意味着，本文内容不仅限于翻墙手段，更在于安全与匿名的加固。

## 名词解释

### [ss-libev](https://shadowsocks.org/en/spec/Implementations.html)

适用于嵌入式设备和低端机的轻量级 C 语言实现。数千个连接的占用空间非常小（几兆字节）。

![](https://ws1.sinaimg.cn/large/0076LFY4ly1fzxokwh97bj30q60g63zn.jpg)

### [AES-NI](https://zh.wikipedia.org/wiki/AES%E6%8C%87%E4%BB%A4%E9%9B%86)

高级加密标准指令集（或称英特尔高级加密标准新指令，简称 AES-NI）是一个 x86 指令集架构的扩展，用于 Intel 和 AMD 微处理器，由 Intel 在 2008 年 3 月提出。该指令集的目的是改进应用程序使用高级加密标准（AES）执行加密和解密的速度。

### [AEAD 加密](https://shadowsocks.org/en/spec/AEAD-Ciphers.html)

AEAD 代表带有关联数据的经过身份验证的加密。AEAD 密码同时提供机密性，完整性和真实性。它们在现代硬件上具有出色的性能和功效。用户应尽可能使用 AEAD 密码。

建议使用以下 AEAD 密码。兼容的 Shadowsocks 实现必须支持 AEAD_CHACHA20_POLY1305。具有硬件 AES 加速的设备的实现还应实现 AEAD_AES_128_GCM，AEAD_AES_192_GCM 和 AEAD_AES_256_GCM。

![](https://ws1.sinaimg.cn/large/0076LFY4ly1fzxofj1t59j30q605dmxf.jpg)

### [TFO](https://zh.wikipedia.org/wiki/TCP%E5%BF%AB%E9%80%9F%E6%89%93%E5%BC%80)

TCP 快速打开（英语：TCP Fast Open，简称 TFO）是对计算机网络中传输控制协议（TCP）连接的一种简化握手手续的拓展，用于提高两端点间连接的打开速度。

### [WebSocket](https://zh.wikipedia.org/wiki/WebSocket)

WebSocket 是一种在单个 TCP 连接上进行全双工通信的协议。WebSocket 通信协议于 2011 年被 IETF 定为标准 RFC 6455，并由 RFC7936 补充规范。WebSocket API 也被 W3C 定为标准。

WebSocket 使得客户端和服务器之间的数据交换变得更加简单，允许服务端主动向客户端推送数据。在 WebSocket API 中，浏览器和服务器只需要完成一次握手，两者之间就直接可以创建持久性的连接，并进行双向数据传输。

### [Nginx](https://zh.wikipedia.org/wiki/Nginx)

Nginx（发音同 engine x）是异步框架的 Web 服务器，也可以用作反向代理，负载平衡器和 HTTP 缓存。该软件由 Igor Sysoev 创建，并于 2004 年首次公开发布。同名公司成立于 2011 年，以提供支持。

Nginx 是免费的开源软件，根据类 BSD 许可证的条款发布。一大部分 Web 服务器使用 Nginx，通常作为负载均衡器。

### [TLS](https://zh.wikipedia.org/wiki/%E5%82%B3%E8%BC%B8%E5%B1%A4%E5%AE%89%E5%85%A8%E6%80%A7%E5%8D%94%E5%AE%9A)

传输层安全性协议（英语：Transport Layer Security，缩写作 TLS），及其前身安全套接层（Secure Sockets Layer，缩写作 SSL）是一种安全协议，目的是为互联网通信提供安全及数据完整性保障。网景公司（Netscape）在 1994 年推出首版网页浏览器，网景导航者时，推出 HTTPS 协议，以 SSL 进行加密，这是 SSL 的起源。IETF 将 SSL 进行标准化，1999年公布第一版 TLS 标准文件。随后又公布 RFC 5246（2008年8月）与 RFC 6176（2011年3月）。在浏览器、电子邮件、即时通信、VoIP、网络传真等应用程序中，广泛支持这个协议。主要的网站，如Google、Facebook等也以这个协议来创建安全连线，发送数据。当前已成为互联网上保密通信的工业标准。

SSL 包含记录层（Record Layer）和传输层，记录层协议确定传输层数据的封装格式。传输层安全协议使用 X.509 认证，之后利用非对称加密演算来对通信方做身份认证，之后交换对称密钥作为会谈密钥（Session key）。这个会谈密钥是用来将通信两方交换的数据做加密，保证两个应用间通信的保密性和可靠性，使客户与服务器应用之间的通信不被攻击者窃听。

### [DNS over HTTPS](https://zh.wikipedia.org/wiki/DNS_over_HTTPS)

DNS over HTTPS（DoH）是一个进行安全化的域名解析的方案，当前尚处于实验性阶段。其意义在于以加密的 HTTPS 协议进行 DNS 解析请求，避免原始 DNS 协议中用户的 DNS 解析请求被窃听或者修改的问题（例如中间人攻击）来达到保护用户隐私的目的。Google 及 Mozilla 基金会正在测试这一协议，作为其提高网络安全性的努力的一部分。

## Shadowsocks

> A secure socks5 proxy,
> designed to protect your Internet traffic.

### 配置文件完整样例

```
{
    "server": "144.202.112.164",
    "server_port": 8443,
    "local_address": "127.0.0.1",
    "local_port": 1080,
    "password": "fff33389-4739-496e-a73f-a35d408b0ff6",
    "timeout": 300,
    "method": "chacha20-ietf-poly1305",
    "mode": "tcp_and_udp",
    "fast_open": false,
    "wokers": 1
}
```

`server` 和 `password` 必须自行更改

| Name          | Explanation                                                                |
| :------------ | :------------------------------------------------------------------------- |
| server        | 服务端监听地址（IPv4 或 IPv6）                                             |
| server_port   | 服务端端口                                                                 |
| local_address | 本地监听地址，缺省为 127.0.0.1                                             |
| local_port    | 本地监听端口                                                               |
| password      | 用以加密的密匙，可由 [UUID Generator](https://www.uuidgenerator.net/) 生成 |
| timeout       | 超时时间（秒）                                                             |
| method        | 参阅本文 [AEAD 加密](#AEAD-%E5%8A%A0%E5%AF%86)                             |
| mode          | 设置为 tcp 或 udp 或 tcp_and_udp                                           |
| fast_open     | 是否启用 TFO                                                               |
| wokers        | worker 数量，缺省为 1，仅在 Unix/Linux 系统下有效                          |

## Project V

> Project V 是一个工具集合，它可以帮助你打造专属的基础通信网络。Project V 的核心工具称为 `V2Ray`，其主要负责网络协议和功能的实现，与其它 Project V 通信。V2Ray 可以单独运行，也可以和其它工具配合，以提供简便的操作流程。

有闲钱的先生们可以考虑资助 V2Ray 发展，作者开发也蛮不容易的 :-)

https://v2ray.com/chapter_00/02_donate.html

官方站点已被 GFW 进行 DNS 污染，可修改为清华 DNS `101.6.6.6` 以进行访问。

### 推荐手动部署

阅读手册可以帮助你手动部署，本人坚持并推荐手动部署以进行各种高级操作，以下站点可供参考 :)

* [官方站点](https://v2ray.com/)
* [白话文教程](https://toutyrater.github.io/)
* [Kiri 的模板](https://github.com/KiriKira/vTemplate)

这里对本人的操作进行简易讲解。

```
USER <-GFW-> [VMess（ws）+ Nginx（tls）] <---> [Shadowsocks]
```

通过本地 V2Ray 中转，原本想要直接连接到 Shadowsocks 协议的代理服务器，但被告知需要先连接到中转服务器，当中真实的 Web 站点反代其本地 V2Ray 基于 VMess 协议的 WebSocket 传输方式，连接成功后，再将数据转发到下一级 Shadowsocks 协议的代理服务器。

## 服务性能优化

### 仅开启 TFO

若服务器和客户端皆部署在 Linux 内核 >= 3.7.1 版本上，则可以开启 TCP Fast Open 以降低延迟，当然，这也需要相关服务的支持。

```
# echo "net.ipv4.tcp_fastopen = 3" > /etc/sysctl.d/tfo.conf
# sysctl --system
```

### 仅开启 BBR

```
# echo "net.core.default_qdisc = fq" > /etc/sysctl.d/bbr.conf
# echo "net.ipv4.tcp_congestion_control = bbr" >> /etc/sysctl.d/bbr.conf
# sysctl --system
```

### 同时开启 TFO + BBR and 较全面提升

```
# vim /etc/sysctl.d/default.conf

# 最大打开文件
fs.file-max = 51200
# 最大读取缓冲区
net.core.rmem_max = 67108864
# 最大写入缓冲区
net.core.wmem_max = 67108864
# 默认读取缓冲区
net.core.rmem_default = 65536
# 默认写入缓冲区
net.core.wmem_default = 65536
# 最大处理器输入队列
net.core.netdev_max_backlog = 4096
# 最大积压量
net.core.somaxconn = 4096

# 抵御 SYN 洪水攻击
net.ipv4.tcp_syncookies = 1
# 安全时重用 timewait 套接字
net.ipv4.tcp_tw_reuse = 1
# 关闭快速 timewait 套接字回收
net.ipv4.tcp_tw_recycle = 0
# FIN 超时时间
net.ipv4.tcp_fin_timeout = 30
# keepalive 间隔时间
net.ipv4.tcp_keepalive_time = 1200
# 出站端口范围
net.ipv4.ip_local_port_range = 10000 65000
# 最大 SYN 积压
net.ipv4.tcp_max_syn_backlog = 4096
# 由系统同时持有的最大 timewait 套接字
net.ipv4.tcp_max_tw_buckets = 5000
# 开启 TCP Fast Open
net.ipv4.tcp_fastopen = 3
# TCP 接收缓冲区
net.ipv4.tcp_rmem = 4096 87380 67108864
# TCP 写入缓冲区
net.ipv4.tcp_wmem = 4096 65536 67108864
# 开启 MTU 探测
net.ipv4.tcp_mtu_probing = 1

# 开启忽略 ICMP 请求
#net.ipv4.icmp_echo_ignore_all = 1

# 适用于高延迟网络
#net.ipv4.tcp_congestion_control = hybla

# 对于低延迟网络，用 cubic 代替
#net.ipv4.tcp_congestion_control = cubic

# BBR
net.core.default_qdisc = fq
net.ipv4.tcp_congestion_control = bbr
```

```
# sysctl --system
```

### 增加系统文件描述符的最大限数

```
# mkdir /etc/security/limits.d
```

<所有用户> <软限制和硬限制> <文件描述符> <整型数值>

```
# vim /etc/security/limits.d/default.conf

* - nofile 51200
```

重启生效

```
# shutdown -r now
```

也可以仅在当前环境下立即生效

```
# ulimit -n 51200
# ulimit -Hn 51200
```

### AES 系列加密方式优势对比

选择 AES 而非 Chacha20 取决于进行数据传输的设备是否皆支持 AES-NI，检测指令如下：

```
$ lsmod | grep aes

aesni_intel           200704  2
aes_x86_64             20480  1 aesni_intel
crypto_simd            16384  1 aesni_intel
cryptd                 28672  4 crypto_simd,ghash_clmulni_intel,aesni_intel
glue_helper            16384  1 aesni_intel
```

```
$ cat /proc/cpuinfo | grep -o -m1 aes

aes
```

效果如上所示则表示支持，这说明更应该使用 AES 系列加密方式。

在双方皆支持 AES-NI 的设备上的性能：

```
aes-128-gcm > aes-192-gcm > aes-256-gcm > chacha20-ietf-poly1305
```

在任何一方不支持 AES-NI 的设备上的性能：

```
chacha20-ietf-poly1305 > aes-128-gcm > aes-192-gcm > aes-256-gcm
```

现在可以知道，支持 AES-NI 的情况下使用 AES 系列加密方式具有显著优势，但同情况下，AES 系列加密方式内应如何选择为好？

下面是本人亲自编译后修改并使用「[性能测试脚本](https://github.com/v2ray/experiments/tree/master/benchmark)」进行的简易测试结果（V2Ray 内置 Shadowsocks 协议，AES-128-GCM 与 AES-256-GCM，各 50 次，忽略并发连接数，速度 MB/s）。

| 50     | AES-128-GCM | AES-256-GCM |
| :----: | :---------: | :---------: |
| 平均数 | 666.16      | 649.3       |
| 中位数 | 602         | 585         |
| 众数   | 568         | 568         |
| 最大值 | 1706        | 1137        |
| 最小值 | 538         | 538         |

由于 AES-192-GCM 具有过渡性，故忽略。

可以看出，AES-128-GCM 具有比 AES-256-GCM 更高效的加密速度，但在实际使用中，不会因此而造成显著的性能优化。换句话说，AES-256-GCM 以较 AES-128-GCM 而言微乎其微的性能损失，换来的是更难以破解的信息加密，何乐而不为。

### Nginx 微调

在使用 Nginx 代理 WebSocket 连接的 V2Ray 配置下，可以进行部分调整以提高性能。

```
# 分配 Worker 的 CPU 核心数量
worker_processes 1;

events {
    # 每 Worker 进程在同一时间的最大连接数
    worker_connections 51200;
    # 收到一个新连接后接受尽可能多的连接
    multi_accept on;
}

http {
    # 散列表最大数量
    types_hash_max_size 4096;
    # 服务器名字哈希桶的数量
    server_names_hash_bucket_size 128;

    # 关闭访问日志
    access_log off;
    # 关闭显示版本号
    server_tokens off;

    # 开启提高静态资源托管效率
    sendfile on;
    # 开启数据包累计到一定大小后发送
    tcp_nopush on;
    # 开启尽快发送数据
    tcp_nodelay on;

    # 每个 TCP 连接最多保持的时间
    keepalive_timeout 60;

    # 开启 Gzip
    gzip on;
    # 开启输出 Vary 响应头
    gzip_vary on;
    # Gzip 压缩等级
    gzip_comp_level 6;
    # Gzip buffers 压缩后的数量和大小
    gzip_buffers 16 8k;
    # Gzip 最小长度
    gzip_min_length 1000;

    # SSL 安全措施
    ssl_ciphers "EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH";
    ssl_protocols TLSv1.2 TLSv1.3;

    server {
        # 开启 HTTP/2
        listen 443 ssl http2;
        server_name domain.chain;

        # SSL 证书
        ssl_certificate /srv/http/domain.chain.crt;
        ssl_certificate_key /srv/http/domain.chain.key;

        # 共享 SSL 会话缓存
        ssl_session_cache shared:SSL:10m;
        # 共享 SSL 会话超时时间
        ssl_session_timeout 60m;

        # 开启 SSL 会话票
        ssl_session_tickets on;

        # 开启 OCSP stapling 策略
        ssl_stapling on;
        ssl_stapling_verify on;
        resolver 8.8.8.8 8.8.4.4 valid=300s;
        resolver_timeout 5s;

        # 开启 HSTS
        add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
    }
}
```

## 通过 dnscrypt-proxy 搭建 DoH

```
# pacman -S dnscrypt-proxy
```

### 基本配置

```
# vim /etc/dnscrypt-proxy/dnscrypt-proxy.toml

server_names = ['cloudflare', 'cloudflare-ipv6']

listen_addresses = []
```

此两行配置即可保证正常运行 :)

```
# systemctl enable dnscrypt-proxy.socket --now
```

### 加快后备解析器效率

[TUNA DNS666 域名查询服务](https://tuna.moe/help/dns/)

```
fallback_resolver = '101.6.6.6:5353'
```

### 防止全局代理后断开连接

```
cache_min_ttl = 86400
```

## 通过 user.js 强化 Firefox 隐私

https://github.com/pyllyukko/user.js

![](https://ws1.sinaimg.cn/large/0076LFY4ly1fzx1qs4whoj30iu077q3n.jpg)

### 推荐[使用 git](https://github.com/pyllyukko/user.js#updating-using-git) 进行部署

部署前需要[刷新 Firefox —— 重置附加组件和设置](https://support.mozilla.org/zh-CN/kb/refresh-firefox-reset-add-ons-and-settings)

```
$ cd ~/.mozilla/firefox
$ git clone https://github.com/pyllyukko/user.js.git
$ cd XXXXXXXX.your_profile_name
$ ln -s ../user.js/user.js user.js
```

## Tor

### 参考

* [如何隐藏你的踪迹，避免跨省追捕[5]：用多重代理隐匿公网 IP](https://program-think.blogspot.com/2012/03/howto-cover-your-tracks-5.html)
* [“如何翻墙”系列：关于 Tor 的常见问题解答](https://program-think.blogspot.com/2013/11/tor-faq.html)

### 基本配置

```
Socks5Proxy 127.0.0.1:1080
ExcludeNodes {cn},{hk},{mo},{kp},{ir},{sy},{pk},{cu},{vn}
StrictNodes 1
```

对照说明

```
# Tor 将通过 127.0.0.1:1080 SOCKS 5 代理进行所有 OR 连接
# 排除这些国家/地区的节点
# 强制执行
```

* {cn} 大陆
* {hk} 香港
* {mo} 澳门
* {kp} 北朝鲜
* {ir} 伊朗
* {sy} 叙利亚
* {pk} 巴基斯坦
* {cu} 古巴
* {vn} 越南

以上国家/地区很可能存在大量蜜罐节点。
