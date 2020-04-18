---
title: V2Ray 配合 Tor
toc: true
date: 2019-05-03 16:55:20
categories: 技术
tags:
  - V2Ray
  - Tor
---

來自於某次無聊時間的隨想：能否通過 V2Ray 直接流入 Tor 網絡呢？

先在服務器上安裝 Tor

```
# pacman -S tor
```

修改 Tor 配置

```
/etc/tor/torrc

ExcludeNodes {cn},{hk},{mo},{kp},{ir},{sy},{pk},{cu},{vn}
StrictNodes 1
```

啓動

```
# systemctl enable tor --now
```

端口是在 127.0.0.1:9050 哦

## 流入 Tor

### 通過客戶端配置

先試試直接通過客戶端的 V2Ray 來讓服務端流入 Tor 網絡

```
...
    "outbounds": [
        {
            "protocol": "socks",
            "settings": {
                "servers": [
                    {
                        "address": "127.0.0.1",
                        "port": 9050,
                        "level": 0
                    }
                ]
            },
            "proxySettings": {
                "tag": "transit"
            }
        },
        {
            ...
            "tag": "transit"
        }
    ]
...
```

可行，再試試直接讓服務端的 V2Ray 來中轉？

### 通過服務端配置

```
...
    "outbounds": [
        {
            "protocol": "socks",
            "settings": {
                "servers": [
                    {
                        "address": "127.0.0.1",
                        "port": 9050,
                        "level": 0
                    }
                ]
            }
        }
    ]
...
```

完美，但現在有一個問題是，如果設定通過 V2Ray 來進行遠程域名解析的話，會同樣流經 Tor 網絡，導致解析域名的速度過於緩慢。

## 域名解析提速方案

1. 在 PC 設備或局域網內，可以考慮搭建並使用 DoH 來解決，即不通過 V2Ray 進行域名解析。
2. 全類型設備通用手段，將 DNS 解析通過 V2Ray 路由功能引入其它服務器。

## 流經 Tor

最終流入 Tor 匿名網絡會再次產生一個問題：節點跳啊跳，可能你突然跳到的一個節點剛剛好就無法訪問 e-hentai 了呢。

怎麼辦呢？既然關鍵在於一個固定的節點，那就讓流入 Tor 的流量變成流經好了，同時還要保證訪問 .onion 時不會因流出 Tor 而導致無法訪問，這裏以 Shadowsocks 爲最終節點。

```
...
    "routing": {
        "strategy": "rules",
        "settings": {
            "rules": [
                {
                    "type": "field",
                    "domain": [
                        "regexp:\\.onion$"
                    ],
                    "outboundTag": "tor"
                }
            ]
        }
    },
...
    "outbounds": [
        {
            "protocol": "shadowsocks",
            "settings": {
                "servers": [
                    {
                        ...
                    }
                ]
            },
            "proxySettings": {
                "tag": "tor"
            }
        },
        {
            "protocol": "socks",
            "settings": {
                "servers": [
                    {
                        "address": "127.0.0.1",
                        "port": 9050,
                        "level": 0
                    }
                ]
            },
            "tag": "tor"
        }
    ]
...
```

選擇的 Shadowsocks 節點儘量要開啓 BBR，方便提速。

不過，也可以考慮最終流入 CDN 節點，這還需要進一步修改。
