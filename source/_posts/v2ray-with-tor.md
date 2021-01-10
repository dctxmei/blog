---
title: V2Ray 與 Tor
date: 2019-05-03 16:55:20
tags:
  - V2Ray
  - Tor
categories: 技術
toc: true
---

V2Ray，是可以和各種軟體互相配合的，這就包含了 Tor。

如果你注重網路隱私的話，本文或許能爲你提供一些參考。

## 結構

本文採用 V2Ray 的多文件配置結構：

```
.
├── 00_log.json
├── 01_api.json
├── 02_dns.json
├── 03_routing.json
├── 04_policy.json
├── 05_inbounds.json
├── 06_outbounds.json
├── 07_transport.json
├── 08_stats.json
└── 09_reverse.json

0 directories, 10 files
```

## 在伺服器上安裝 Tor

```
# apt install tor
```

### 修改 Tor 的配置

```
# vi /etc/tor/torrc

# A list of identity fingerprints, country codes, and address patterns of nodes to avoid when building a circuit
ExcludeNodes {by},{cn},{cu},{hk},{ir},{kp},{mo},{pk},{ru},{sy},{vn}
# If StrictNodes is set to 1, Tor will treat solely the ExcludeNodes option as a requirement to follow for all the circuits you generate, even if doing so will break functionality for you (StrictNodes applies to neither ExcludeExitNodes nor to ExitNodes, nor to MiddleNodes)
StrictNodes 1
```

### 啓用並啓動 Tor

```
# systemctl enable tor
# systemctl start tor
```

## 通過 V2Ray 直接流入 Tor

```json
# vi /usr/local/etc/v2ray/06_outbounds.json

{
    "outbounds": [
        {
            "protocol": "socks",
            "settings": {
                "servers": [
                    {
                        "address": "127.0.0.1",
                        "port": 9050
                    }
                ]
            }
        }
    ]
}
```

## 流經而非最終

最終流入 Tor 匿名網絡會產生一個問題：

節點的跳動，很可能會導致，突然跳到的一個節點，是無法使用 Google 搜索引擎的。

怎麼辦呢？

既然關鍵在於一個固定的節點，那就讓原本流入 Tor 的流量，變成流經吧。

1. 保證訪問 `.onion` 時，不會因流出 Tor 而導致無法訪問。
2. 保證可以隨時修改最終節點。

那就只能在客戶端層面入手了。

```json
# vi /usr/local/etc/v2ray/03_routing.json

{
    "routing": {
        "rules": [
            {
                "type": "field",
                "domain": [
                    "regexp:\\.onion$"
                ],
                "outboundTag": "proxy"
            }
        ]
    }
}
```

```json
# vi /usr/local/etc/v2ray/06_outbounds.json

{
    "outbounds": [
        {
            "protocol": "vmess",
            "settings": {
                "vnext": [
                    {
                        "address": "1.1.1.1",
                        "port": 443,
                        "users": [
                            {
                                "id": "20be3195-6c4b-44c8-95ce-c6f2d5d5b073"
                            }
                        ]
                    }
                ]
            },
            "proxySettings": {
                "tag": "proxy"
            }
        },
        {
            "protocol": "http",
            "settings": {
                "servers": [
                    {
                        "address": "127.0.0.1",
                        "port": 7890
                    }
                ]
            },
            "tag": "proxy"
        }
    ]
}
```
