+++
title = "对 Linux 下触控板按键、加速和手势的优化（libinput）"
date = 2020-09-11

[extra.comments]
issue_id = 42

[[extra.comments.addition]]
user = { login = "aiviciizhx", avatar_url = "https://secure.gravatar.com/avatar/f359ec637fed54bf737d0582c1240ed3?d=retro" }
created_at = "2022-03-29T05:54:45Z"
body = "很好的一篇"

[[extra.comments.addition]]
user = { login = "Smile", avatar_url = "https://secure.gravatar.com/avatar/af05c093f9f4982f6ced97cff9feb5aa?d=retro" }
created_at = "2023-04-06T04:18:07Z"
body = "配置文件目录应该是这个吧/usr/share/X11/xorg.conf.d"
+++

我们都知道 macOS 下的触控板体验非常好，而 Windows 下的触控板体验最近几年靠着厂商的跟进和 Windows Precision Drivers 也进步了不少。其实在 Linux 的 Xorg 下想要达到类似的体验也非常简单。

Xorg 下经常用到的触控板驱动有 [Synaptics（已停止维护）](https://wiki.archlinux.org/index.php/Touchpad_Synaptics)、[libinput](https://wiki.archlinux.org/index.php/Libinput) 和 [mtrack](https://github.com/p2rkw/xf86-input-mtrack)。这篇文章将会用 libinput 来优化改善触控板的按键、加速和手势设置。

<!--more-->

# 所需软件包

首先需要的肯定是驱动本体，libinput 在各个发行版的官方仓库里都有打包，可以直接安装。

实现触控板手势需要 [libinput-gestures](https://github.com/bulletmark/libinput-gestures)，直接编译安装， Arch 系可以直接用 AUR 安装。

（可选）可以安装 libinput-gestures 的 GUI 前端 [gestures](https://gitlab.com/cunidev/gestures) 用来设置 libinput-gestures，但我认为实在没有必要，因为配置文件改起来也不难。

# 触控板加速与按键设置

首先查看 `/etc/X11/xorg.conf.d/` 下有没有已有的触控板配置文件，具体是看 Section 中是否有 `Identifier "touchpad"`。如果有就把这个 Section 删掉（最好先备份以防出问题）。
新建一个配置文件，优先级与文件名随意，如 `20-touchpad.conf`，内容如下：

    Section "InputClass"
        # 限定此配置文件只适用于触控板
        Identifier "touchpad"
        MatchIsTouchpad "on"
    
        # 设定驱动为 libinput
        Driver "libinput"
    
        # 在后面插入具体配置
        # ...
    
    EndSection

之后在文件中填入需要的配置。

## 灵敏度与鼠标加速

        # 使用预设的触控板加速配置 2
        Option "AccelerationProfile" "2"
    
        # 设置灵敏度为 0.05
        Option "Sensitivity" "0.05"

如果用起来不顺手，你可以尝试调整 `"Sensitivity"` 的值，毕竟每个触控板的大小和素质都不同。

## 轻触即点击

默认情况下 Xorg 不会将“轻点触控板”识别为点击，想要开启轻触即点击，加入：

        Option "Tapping" "on"

## 修改单指、双指、三指对应的左、中、右键

默认情况下 Xorg 会将单指视为左键、双指视为中键、三指视为右键，需要修改可以加入：

        Option "TappingButtonMap" "lrm"

值中的 `l` 是 Left，`r` 是 Right，`m` 是 Mid，顺序为单、双、三指。

## 反转触控板滚动的方向

默认情况下 Xorg 的触控板滚动方向与 Windows 和 macOS 相反，觉得别扭可以开启“自然滚动”：

        Option "NaturalScrolling" "on"

最终，我的配置文件 `20-touchpad.conf` 的内容是：

    Section "InputClass"
        Identifier "touchpad"
        MatchIsTouchpad "on"
        Driver "libinput"
        Option "AccelerationProfile" "2"
        Option "Sensitivity" "0.05"
        Option "Tapping" "on"
        Option "TappingButtonMap" "lrm"
        Option "NaturalScrolling" "on"
    EndSection

# 触控板手势

触控板手势最好配合WM的快捷键使用，例如将四指左右扫动绑定为 Ctrl+Right/Left，同时将 WM 的工作区切换也设置为同样的快捷键。
修改 libinput-gestures 的配置文件，`/etc/libinput-gestures.conf` 或是 `~/.config/libinput-gestures.conf`。

我的 `libinput-gestures.conf`：

    # 识别阈值
    swipe_threshold 0
    
    # 切换工作区
    gesture swipe left 4 xdotool key Ctrl+Right
    gesture swipe right 4 xdotool key Ctrl+Left
    
    # 切换窗口
    gesture swipe left 3 xdotool key Alt+Right
    gesture swipe right 3 xdotool key Alt+Left
    
    # 显示/隐藏所有窗口
    gesture swipe down 4 xdotool key Super+a
    
    # 放大与缩小
    gesture pinch in 2 xdotool key Ctrl+minus
    gesture pinch out 2 xdotool key Ctrl+plus

最后启动 libinput-gestures，手势就会生效。

# 开关触控板

在不用触控板时可以将其关掉。利用 `xinput enable/disable TOUCHPAD_ID` 可以开关触控板。

可以写一个脚本，执行就会开关触控板：

    #!/bin/bash
    TOUCHPAD_ID=`xinput list | grep "Touchpad" | sed -n -e 's/^.*id=//p'|cut -f1`
    if xinput list $TOUCHPAD_ID | grep -q 'This device is disabled'; then
	xinput enable $TOUCHPAD_ID
    else
	xinput disable $TOUCHPAD_ID
    fi

再绑定一个键盘快捷键，就可以轻松开关触控板了。
需要注意，这个脚本不会停止 libinput-gestures 识别触控板手势，如果要把手势同时禁用掉，就在脚本里分别加入启动、停止 libinput-gestures 的命令即可。
