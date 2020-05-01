---
title: Oh My Kali Linux
date: 2017-03-03 19:43:17
tags:
  - Linux
  - Kali Linux
categories: 技術
toc: true
---

**本文內容已過時！**

> Kali Linux 是基於 Debian 的 Linux 發行版，設計用於數字鑑識和滲透測試。

* Image: [Kali Linux](https://kali.org/) 64/32 bit Light

### 基礎篇

#### 修改啟動時間

```
sed 's/GRUB_TIMEOUT=5/GRUB_TIMEOUT=2/' -i /etc/default/grub
update-grub
```

#### 修改網絡設置

```
sed 's/lcp-echo-failure 4/lcp-echo-failure 60/' -i /etc/ppp/options
```

#### 卸載默認桌面環境

```
apt-get autoremove --purge gnome* xfce* xterm*
```

#### 設置 Root 下開啟聲卡驅動

```
systemctl --user enable pulseaudio
cat << EOF > /etc/default/pulseaudio
PULSEAUDIO_SYSTEM_START=1
DISALLOW_MODULE_LOADING=0
EOF
```

### 聯網篇

```
wpa_supplicant -B -i interface -c <(wpa_passphrase MYSSID passphrase)
dhclient interface
```

### 鏡像源篇

```
cat << EOF > /etc/apt/sources.list
#deb http://download.virtualbox.org/virtualbox/debian jessie contrib
#deb http://packages.deepin.com/deepin unstable contrib main non-free
# For source package access, uncomment the following line
#deb-src http://packages.deepin.com/deepin unstable contrib main non-free
#deb http://ftp.cn.debian.org/debian/ jessie contrib main non-free
# For source package access, uncomment the following line
#deb-src http://ftp.cn.debian.org/debian/ jessie contrib main non-free
deb http://http.kali.org/kali kali-rolling contrib main non-free
# For source package access, uncomment the following line
deb-src http://http.kali.org/kali kali-rolling contrib main non-free
EOF
```

```
apt-get clean
apt-get update
```

### 日常篇

#### 安裝日常使用軟件

```
apt-get install powerline zsh gcc make go-mtpfs vim axel hping3 apache2-utils alsa-utils mplayer
```

#### 設置 Zsh 為默認終端

```
chsh -s /bin/zsh
```

```
cat << EOF >> /etc/zsh/zprofile

emulate sh -c 'source /etc/profile'
EOF
```

```
cat << EOF >> /etc/zsh/zshrc

# 開啟自動補全
autoload -U compinit
compinit
# 啟動使用方向鍵控制的自動補全
zstyle ':completion:*' menu select
# 啟動命令行別名的自動補全
setopt completealiases
# 開啟歷史記錄
HISTFILE="\$HOME/.histfile"
HISTSIZE=1000
SAVEHIST=1000
# 消除歷史記錄中的重複條目
setopt HIST_IGNORE_DUPS
# 鎖定/解鎖終端
ttyctl -f
# 命令提示符
. /usr/share/powerline/bindings/zsh/powerline.zsh
# 刷新自動補全
zstyle ':completion:*' rehash true
EOF
```

### 桌面環境篇

#### 安裝 i3wm 等基本軟件

```
apt-get install xorg i3-wm i3lock conky suckless-tools feh sakura xcompmgr fonts-font-awesome libappindicator3-1 chromium-l10n ibus-pinyin simplescreenrecorder
```

#### 安裝 Lantern

```
# 上述 libappindicator3-1 為修復 Lantern 依賴
axel -n 9 'https://raw.githubusercontent.com/getlantern/lantern-binaries/master/lantern-installer-beta-64-bit.deb'
dpkg -i lantern-installer-beta-64-bit.deb
```

#### 卸載 Firefox

```
apt-get autoremove --purge firefox*
# 若在此之前運行過 Firefox, 執行 `rm -r /usr/lib/mozilla`
dpkg -l | grep ^rc | awk '{print $2}' | xargs dpkg -P
```

#### 設置 Chromium 可在 Root 運行

```
sed 's/exec $LIBDIR\/$APPNAME $CHROMIUM_FLAGS "$@"/exec $LIBDIR\/$APPNAME $CHROMIUM_FLAGS "$@" --no-sandbox --user-data-dir/' -i /usr/bin/chromium
```

#### 系統升級

```
apt-get dist-upgrade
```

#### 換為 VirtualBox 鏡像源

```
sed -e '8s/^/#/' -e '10s/^/#/' -e '1s/#//' -i /etc/apt/sources.list
apt-get clean
apt-get update
```

#### 下載 VirtualBox

```
apt-get download virtualbox-5.1
```

#### 換為 Deepin 鏡像源

```
sed -e '1s/^/#/' -e '2s/#//' -i /etc/apt/sources.list
apt-get clean
apt-get update
```

#### 安裝網易雲音樂 or VirtualBox

```
apt-get autoremove --purge libqt5gui5*
apt-get install netease-cloud-music
apt-get install virtualbox-5.1*.deb
apt-get -f install
```

#### 設置網易雲音樂可在 Root 運行

```
sed '7s/\/usr\/lib\/netease-cloud-music\/netease-cloud-music/\/usr\/lib\/netease-cloud-music\/netease-cloud-music --no-sandbox/' -i /usr/bin/netease-cloud-music
```

#### 換回 Kali Linux 鏡像源

```
sed -e '2s/^/#/' -e '8s/#//' -e '10s/#//' -i /etc/apt/sources.list
apt-get clean
apt-get update
```

#### 設置 i3wm

```
cat << EOF > /etc/conky/conky.conf
conky.config = {
	-- 防止閃爍
	double_buffer = true,
	-- 輸出文本到標準輸入
	out_to_console = true,
	-- 不輸出到 X
	out_to_x = false,
	-- 縮寫單位字符
	short_units = true,
	-- 刷新時間
	update_interval = 0.1
}

conky.text = [[
 \${upspeed wlp3s0}/s \\
 \${downspeed wlp3s0}/s \\
 \${addr wlp3s0} \\
 \${fs_free /} \\
 \$memperc}% \\
\${exec amixer | grep -A 6 'Master' | sed -nr 's/.*\[([0-9]+%)\].*\[(on|off)\]/\2 \1/p' | sed -n '2p' | sed -r 's/on( [4-9][0-9])/\1/' | sed -r 's/on( [0-9])/\1/' | sed -e 's/ 100%/ 100%/' -e 's/ 0%/ 0%/' -e 's/off.*/X/'} \\
 \${time %H:%M} \\
 \${battery_percent}% \\
 \${nodename}
]]
EOF
```

```
sed '166,179d' -i /etc/i3/config
sed -r 's/workspace ([0-9])/workspace $WS\1/' -i /etc/i3/config
sed 's/i3status/conky/' -i /etc/i3/config
```

```
cat << EOF >> /etc/i3/config
bar {
        # 調用 Conky
        status_command conky
        # 頂置
        position top
        # 隱藏工作區名 1:, 2: ......
        strip_workspace_numbers yes
        colors {
            # 工作區背景
            background #222222

                               # 邊框    背景    文字
            # 工作區焦點
            focused_workspace  #000000 #000000 #FFFFFF
            # 工作區無焦點且可見
            inactive_workspace #222222 #222222 #FFFFFF
            # 工作區包含緊急提示集的窗口
            urgent_workspace   #999999 #999999 #FFFFFF
        }
}
EOF
```

```
cat << EOF >> /etc/i3/config

# 自定義
## 關閉屏幕保護
exec --no-startup-id xset s off
## 壁紙
exec --no-startup-id feh --bg-scale "\$HOME/圖片/西行妖 & 幽幽子.png"
## 終端透明
exec --no-startup-id xcompmgr
## 開啟/關閉音量
bindsym XF86AudioMute exec amixer set Master toggle
## 減少音量
bindsym XF86AudioLowerVolume exec amixer set Master 2%-
## 增加音量
bindsym XF86AudioRaiseVolume exec amixer set Master 2%+
## 抓取桌面
bindsym Print exec scrot "\$HOME/圖片/%Y-%m-%d_%H:%M:%S.png"
## 9 號 Monaco 字體
font pango:Monaco 9
set \$WS1 1:
set \$WS2 2:
set \$WS3 3:
set \$WS4 4:
set \$WS5 5:
set \$WS6 6:
set \$WS7 7:
set \$WS8 8:
set \$WS9 9:
set \$WS10 10:
assign [class="Chromium"] \$WS2
assign [class="netease-cloud-music"] \$WS3
assign [class="MPlayer"] \$WS3
assign [class="VirtualBox"] \$WS4
                        # 邊框    背景    文字    提示
# 窗口焦點
client.focused          #000000 #000000 #FFFFFF #000000
# 窗口無焦點
client.unfocused        #222222 #222222 #FFFFFF #222222
# 窗口包含緊急提示集
client.urgent           #999999 #999999 #FFFFFF #999999
set \$mode_system System (l) , (e) , (s) suspend, (h) hibernate, (r) , (Shift+s) 
mode "\$mode_system" {
    bindsym l exec --no-startup-id i3lock -i "\$HOME/圖片/八雲紫 & 幽幽子.png", mode "default"
    bindsym e exec --no-startup-id i3-msg exit, mode "default"
    bindsym s exec --no-startup-id i3lock && systemctl suspend, mode "default"
    bindsym h exec --no-startup-id i3lock && systemctl hibernate, mode "default"
    bindsym r exec --no-startup-id systemctl reboot, mode "default"
    bindsym Shift+s exec --no-startup-id systemctl poweroff, mode "default"  

    # back to normal: Enter or Escape
    bindsym Return mode "default"
    bindsym Escape mode "default"
}
bindsym Mod1+Home mode "\$mode_system"
EOF
```
