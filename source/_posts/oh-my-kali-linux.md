---
title: Oh My Kali Linux
date: 2017-03-03 19:43:17
categories: 技术
tags:
  - Linux
toc: true
---

> Kali Linux 是基于 Debian 的 Linux 发行版,设计用于数字鉴识和渗透测试。

* Image: [Kali Linux](https://kali.org/) 64/32 bit Light
<!--more-->

### 基础篇

#### 修改启动时间

```
sed 's/GRUB_TIMEOUT=5/GRUB_TIMEOUT=2/' -i /etc/default/grub
update-grub
```

#### 修改网络设置

```
sed 's/lcp-echo-failure 4/lcp-echo-failure 60/' -i /etc/ppp/options
```

#### 卸载默认桌面环境

```
apt-get autoremove --purge gnome* xfce* xterm*
```

#### 设置 Root 下开启声卡驱动

```
systemctl --user enable pulseaudio
cat << EOF > /etc/default/pulseaudio
PULSEAUDIO_SYSTEM_START=1
DISALLOW_MODULE_LOADING=0
EOF
```

### 联网篇

```
wpa_supplicant -B -i interface -c <(wpa_passphrase MYSSID passphrase)
dhclient interface
```

### 镜像源篇

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

#### 安装日常使用软件

```
apt-get install powerline zsh gcc make go-mtpfs vim axel hping3 apache2-utils alsa-utils mplayer
```

#### 设置 Zsh 为默认终端

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

# 开启自动补全
autoload -U compinit
compinit
# 启动使用方向键控制的自动补全
zstyle ':completion:*' menu select
# 启动命令行别名的自动补全
setopt completealiases
# 开启历史记录
HISTFILE="\$HOME/.histfile"
HISTSIZE=1000
SAVEHIST=1000
# 消除历史记录中的重复条目
setopt HIST_IGNORE_DUPS
# 锁定/解锁终端
ttyctl -f
# 命令提示符
. /usr/share/powerline/bindings/zsh/powerline.zsh
# 刷新自动补全
zstyle ':completion:*' rehash true
EOF
```

### 桌面环境篇

#### 安装 i3wm 等基本软件

```
apt-get install xorg i3-wm i3lock conky suckless-tools feh sakura xcompmgr fonts-font-awesome libappindicator3-1 chromium-l10n ibus-pinyin simplescreenrecorder
```

#### 安装 Lantern

```
# 上述 libappindicator3-1 为修复 Lantern 依赖
axel -n 9 'https://raw.githubusercontent.com/getlantern/lantern-binaries/master/lantern-installer-beta-64-bit.deb'
dpkg -i lantern-installer-beta-64-bit.deb
```

#### 卸载 Firefox

```
apt-get autoremove --purge firefox*
# 若在此之前运行过 Firefox, 执行 `rm -r /usr/lib/mozilla`
dpkg -l | grep ^rc | awk '{print $2}' | xargs dpkg -P
```

#### 设置 Chromium 可在 Root 运行

```
sed 's/exec $LIBDIR\/$APPNAME $CHROMIUM_FLAGS "$@"/exec $LIBDIR\/$APPNAME $CHROMIUM_FLAGS "$@" --no-sandbox --user-data-dir/' -i /usr/bin/chromium
```

#### 系统升级

```
apt-get dist-upgrade
```

#### 换为 VirtualBox 镜像源

```
sed -e '8s/^/#/' -e '10s/^/#/' -e '1s/#//' -i /etc/apt/sources.list
apt-get clean
apt-get update
```

#### 下载 VirtualBox

```
apt-get download virtualbox-5.1
```

#### 换为 Deepin 镜像源

```
sed -e '1s/^/#/' -e '2s/#//' -i /etc/apt/sources.list
apt-get clean
apt-get update
```

#### 安装网易云音乐 or VirtualBox

```
apt-get autoremove --purge libqt5gui5*
apt-get install netease-cloud-music
apt-get install virtualbox-5.1*.deb
apt-get -f install
```

#### 设置网易云音乐可在 Root 运行

```
sed '7s/\/usr\/lib\/netease-cloud-music\/netease-cloud-music/\/usr\/lib\/netease-cloud-music\/netease-cloud-music --no-sandbox/' -i /usr/bin/netease-cloud-music
```

#### 换回 Kali Linux 镜像源

```
sed -e '2s/^/#/' -e '8s/#//' -e '10s/#//' -i /etc/apt/sources.list
apt-get clean
apt-get update
```

#### 设置 i3wm

```
cat << EOF > /etc/conky/conky.conf
conky.config = {
	-- 防止闪烁
	double_buffer = true,
	-- 输出文本到标准输入
	out_to_console = true,
	-- 不输出到 X
	out_to_x = false,
	-- 缩写单位字符
	short_units = true,
	-- 刷新时间
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
        # 调用 Conky
        status_command conky
        # 顶置
        position top
        # 隐藏工作区名 1:, 2: ......
        strip_workspace_numbers yes
        colors {
            # 工作区背景
            background #222222

                               # 边框    背景    文字
            # 工作区焦点
            focused_workspace  #000000 #000000 #FFFFFF
            # 工作区无焦点且可见
            inactive_workspace #222222 #222222 #FFFFFF
            # 工作区包含紧急提示集的窗口
            urgent_workspace   #999999 #999999 #FFFFFF
        }
}
EOF
```

```
cat << EOF >> /etc/i3/config

# 自定义
## 关闭屏幕保护
exec --no-startup-id xset s off
## 壁纸
exec --no-startup-id feh --bg-scale "\$HOME/图片/西行妖 & 幽幽子.png"
## 终端透明
exec --no-startup-id xcompmgr
## 开启/关闭音量
bindsym XF86AudioMute exec amixer set Master toggle
## 减少音量
bindsym XF86AudioLowerVolume exec amixer set Master 2%-
## 增加音量
bindsym XF86AudioRaiseVolume exec amixer set Master 2%+
## 抓取桌面
bindsym Print exec scrot "\$HOME/图片/%Y-%m-%d_%H:%M:%S.png"
## 9 号 Monaco 字体
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
                        # 边框    背景    文字    提示
# 窗口焦点
client.focused          #000000 #000000 #FFFFFF #000000
# 窗口无焦点
client.unfocused        #222222 #222222 #FFFFFF #222222
# 窗口包含紧急提示集
client.urgent           #999999 #999999 #FFFFFF #999999
set \$mode_system System (l) , (e) , (s) suspend, (h) hibernate, (r) , (Shift+s) 
mode "\$mode_system" {
    bindsym l exec --no-startup-id i3lock -i "\$HOME/图片/八云紫 & 幽幽子.png", mode "default"
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
