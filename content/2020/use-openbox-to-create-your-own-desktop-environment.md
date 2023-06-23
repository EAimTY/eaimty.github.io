+++
title = "使用 Openbox 作为基底打造你自己的 Linux 桌面环境"
date = 2020-09-10

[extra.comments]
issue_id = 41

[[extra.comments.addition]]
user = { login = "cws", html_url = "https://www.kfchy.com", avatar_url = "https://secure.gravatar.com/avatar/?d=retro" }
created_at = "2022-05-17T14:28:31Z"
body = "> 新手学着装了一次，最后因为不会调教字体，总觉得不清晰、丑，放弃了\n\n安装文泉译-微米黑就很好"

[[extra.comments.addition]]
user = { login = "cws", html_url = "https://www.kfchy.com", avatar_url = "https://secure.gravatar.com/avatar/?d=retro" }
created_at = "2022-05-17T14:26:56Z"
body = "> > 新手学着装了一次，最后因为不会调教字体，总觉得不清晰、丑，放弃了\n> \n> 可能你没有设置好字体 fallback？按理说设置字体应该是很简单的\n\n他是\"新手"

[[extra.comments.addition]]
user = { login = "EAimTY", html_url = "https://www.eaimty.com/", avatar_url = "https://secure.gravatar.com/avatar/f64037e002ad7b01fe9ee60351bfd1d2?d=retro" }
created_at = "2022-01-01T07:27:21Z"
body = "> 新手学着装了一次，最后因为不会调教字体，总觉得不清晰、丑，放弃了\n\n可能你没有设置好字体 fallback？按理说设置字体应该是很简单的"

[[extra.comments.addition]]
user = { login = "rc", avatar_url = "https://secure.gravatar.com/avatar/ab61904744b53ca955db5a061786a4e9?d=retro" }
created_at = "2021-12-30T10:12:43Z"
body = "新手学着装了一次，最后因为不会调教字体，总觉得不清晰、丑，放弃了"

[[extra.comments.addition]]
user = { login = "EAimTY", html_url = "https://www.eaimty.com/", avatar_url = "https://secure.gravatar.com/avatar/f64037e002ad7b01fe9ee60351bfd1d2?d=retro" }
created_at = "2020-09-15T15:10:08Z"
body = "> 建议搭配 https://www.csslayer.info/wordpress/linux/why-you-should-not-use-standalone-wm-as-your-desktop/ 食用（x）\n\n毕竟萝卜青菜各有所爱，而且那篇文章已经十年了，且不说时效性，我觉得不会有自己组装桌面环境的人会觉得那篇文章中提到的问题真的是问题吧..."

[[extra.comments.addition]]
user = { login = "lyman", avatar_url = "https://secure.gravatar.com/avatar/1e1a97dc6bc0acd328e9299b20fa0688?d=retro" }
created_at = "2022-11-21T02:18:26Z"
body = "对我这个菜鸟来说，真的很有用！谢谢。"

[[extra.comments.addition]]
user = { login = "Horo", html_url = "https://blog.yoitsu.moe", avatar_url = "https://secure.gravatar.com/avatar/1437f8efee1c746e1e094999d4ecad35?d=retro" }
created_at = "2020-09-14T11:34:54Z"
body = "建议搭配 https://www.csslayer.info/wordpress/linux/why-you-should-not-use-standalone-wm-as-your-desktop/ 食用（x）"
+++

这篇文章会教你如何使用 Openbox 作为基底打造你自己的 Linux 桌面环境。

<!--more-->

很久没有写过这类教程了，如果发现文章中的问题或不足，可以在评论中告诉我😁

# 开始之前

## Openbox 是什么？

对于能够找到这篇文章的你，这应该不是你想问的问题吧...

Openbox 是一个 WM（Window Manager/窗口管理器）用来显示并管理每个 GUI 程序的窗口。
经常被人们提到的 Gnome、KDE 之类的东西是 DE（Desktop Environment/桌面环境）。一个 DE 通常会包括一个 DM（Display Manager/显示管理器，通常用于用户登录并启动桌面环境）、一个 WM（用于显示窗口）、一个 compositor（或许可以翻译为“合成器”？用于渲染特效、透明效果等）和一大堆附加组件（如窗口列表、dock 栏、托盘）。
理论上讲，各个 DE 中的每个部分都可以被替换掉，例如用 LightDM 替换掉 Gnome 自带的 DM：GDM，或用 openbox 替换掉 XFCE 自带的 WM：xfwm。

## 为什么不用已有的桌面环境？

原因如下：

- 打包好的桌面环境总会包含你不需要的组件。这就像你去饭店点了一份不要辣椒的菜，结果上的菜里还是有辣椒，老板说这辣椒是送你的，免费的──你觉得 compositor 影响性能，不想要它，但大多数桌面环境（就算是最小化安装的也一样）都会“免费”送你一个，虽说你可以禁用掉它，但是有你完全用不到的组件在你的系统里，还由于软件包依赖问题无法卸装，这还是挺恶心的。
- 重量级的桌面环境总是会有类似于 Windows 注册表的东西，例如 dconf。一个文本配置文件就能解决的问题，非要搞得这么复杂。
- 你很难搞清桌面环境中每一个组件、每一个软件包的用途，这导致出现 bug 时的问题排查与解决变得非常麻烦复杂。
- 你就是喜欢折腾，你就是喜欢与众不同🥴

自己动手拼凑桌面环境就不会有这些问题。你喜欢 LXDE 的 Panel、Xfce 的终端、Gnome 的截图工具？没有任何问题，你可以你自己喜欢的所有组件放在一起。这就是不使用已有桌面环境的最大优点。

你的桌面环境是什么样的并不重要，重要的是它是否能让你用得顺手，符合你的习惯，提升你的效率。

## 为什么选用 Openbox？

Openbox 可以说是一个较为“传统”的窗口管理器。它并不支持 wayland，只能运行在传统的 X11 上，但这也保证了它的稳定性，并且对 N 卡有更好的支持。
另外，Openbox 在交互上偏向于使用鼠标。虽然你可以按照你的喜好设置一大堆快捷键，但如果你对键盘操作特别钟情，或是讨厌操作鼠标，可能 [i3](https://i3wm.org)（X11）、[sway](https://swaywm.org)（wayland）类的桌面更适合你。

相较于重量级的 DE，如 Gnome、KDE，Openbox 极其轻量，并且基本不存在什么依赖。
对于其他轻量级 WM，openbox 可以说是配置起来最简单的，并且有几乎是最完备的生态和社区。

## 目标与准则

这篇文章会以我的桌面环境的配置为例，详细地介绍如何拼凑一个桌面环境。我会详细介绍我自己的方案，但也会给出其它方案，你可以按照自己的喜好来选择。就算WM相同，两个人的桌面也可以从外观和使用上完全不同。

我自己的准则：
- 选择稳定、不频繁更改功能、但仍在开发中的组件
- 不使用已过时或已停止开发/支持的组件，也不使用有已过时依赖（如python2）的组件
- 尽量使用图形库比较新的组件，也就是尽量不用使用了 GTK2/QT4 的组件
- 效率优先，不在外观与特效上花太多心思，当然成品看起来也不能丑
- 在日常使用上，能用 GUI 解决的操作，绝不用 terminal
- 使用配置起来简单的组件，就是那些就算用直接改配置文件的方式更改设置也不会很难的组件
- 用到的所有组件都可以透过 Arch 官方源与 AUR 安装，当然手动编译也不难

再次强调，你不需要完全照搬我的配置，我也不推荐你这么做，毕竟只有尝试过更多方案才能选择出最适合你的。这正是 Linux 的精髓。

# 配置过程

在这里，假设你的系统是 Arch（其他系统也大同小异，只不过可能需要手动编译一些组件），且系统中还没有安装桌面环境与 X Window System。

下面列出的程序全部都是没有过时或停止开发/支持并且依赖也没有过时的。

## 基础软件包：X Window System、Display Manager 和 Openbox

首先需要的是处在最底层的 X Window System。一般来说只需要 xorg-server 包和它的依赖。
另外，这里还需要安装你的显卡对应的 xf86-video 包，如 xf86-video-intel。如果你的机器同时有核显与独显，最好暂时只安装核显的包。

你还需要一个 DM 来启动 Openbox，我用的是 [LightDM](https://wiki.archlinux.org/index.php/LightDM)，当然还有其它选择，如 [LXDM](https://wiki.archlinux.org/index.php/LXDM)。顺便一提，如果你准备使用 LXDM 的话，要注意它有 GTK2 与 GTK3 两个版本，一般来说选择 GTK3 版本的比较好。
如果你和我一样选择了 LightDM，那么你还需要一个 Greeter 用来在 GUI 下登录。这里有很多选择，可以参考 [Wiki 中的 Greeter 一节](https://wiki.archlinux.org/index.php/LightDM#Greeter)。我使用的是默认的 lightdm-gtk-greeter，主要原因是它可以使用与桌面环境相同的 GTK 主题来保持界面风格的一致。

当然，不用 DM 也是完全可以的。利用 [Xinit](https://wiki.archlinux.org/index.php/Xinit) 你可以在终端中启动 X Window System，并且可以通过切 tty 来实现多用户。

最后，别忘了安装 Openbox。这里最好顺便装上 xterm，因为刚刚安装好的 Openbox 的应用菜单默认是硬编码的，常用的终端只有 xterm 在菜单中，后面将你喜欢的 GUI 终端添加到 Openbox 应用菜单后你可以直接将 xterm 卸装掉。不装 xterm 也没有任何问题，只不过稍微麻烦一点，最初几步中你需要切换到其它 tty 来执行命令。

将你的 DM 设置为自启后，重启电脑，不出意外的话重启后你会进入 DM 的登录界面。登录后，你便可以进入 Openbox。

## 使桌面环境“可用”

进入一个未经配置的 Openbox，你只会看到黑色的背景，没有任何其它东西，鼠标右键会显示应用菜单。

这时你的桌面系统还远远达不到“可用”，至少还需要窗口列表和系统托盘。

### 窗口列表与系统托盘

这里有几种不同方案：

#### 方案一：使用一个 Dock 栏和一个系统托盘

这种情况下，你没有窗口列表来显示已打开的窗口，所以需要一个可以显示并重新打开最小化后的窗口的 Dock 栏，这里列出 2 个可选项：

- [Cairo-Dock](https://wiki.archlinux.org/index.php/Cairo-Dock)──可定制程度比较高，但我个人不太喜欢，因为它不是很轻量，特效太多，而且一些功能需要 compositor 来实现。在我的笔记本（只开了核显）上打开它后散热风扇竟然都会开始转...
- [Plank](https://wiki.archlinux.org/index.php/Plank)──非常轻量化，可定制性也不错，只是缺少工作区切换的功能，但可以通过设置 Openbox 快捷键或使用其它小组件实现

窗口列表实现了，但你还需要一个系统托盘，可以选择 [Stalonetray](https://wiki.archlinux.org/index.php/Stalonetray)，或 [trayer-srg](https://github.com/sargon/trayer-srg)，或 [taffybar](https://github.com/taffybar/taffybar)

#### 方案二：使用一个 Panel

这是我选择的方案。

Panel 一般会包含窗口列表、工作区切换器、系统托盘之类的东西。通常来说，有一个 Panel 已经足够让你的桌面环境“可用”了。
下面列出几个可选的 Panel：

- [tint2](https://wiki.archlinux.org/index.php/Tint2)或许是最大名鼎鼎的独立 Panel，很轻量，也高度可定制化
- [LXPanel](https://wiki.lxde.org/en/LXPanel)是LXDE桌面环境的默认 Panel，也没什么依赖，可以脱离 LXDE 而单独运行，轻量且定制性也不错，有 GTK2 和 GTK3 版本。在我这里 GTK2 版本可以完美运行，但 GTK3 版本系统托盘中的图标貌似有点 bug,不能实时更新。
- [lxqt-panel](https://github.com/lxqt/lxqt-panel)和 LXPanel 差不多，是 LXQt 桌面环境的默认 Panel。
- [Xfce Panel](https://docs.xfce.org/xfce/xfce4-panel/start)是我最终的选择（后面会讲到为什么），它是 xfce 桌面环境的默认 Panel，也没什么依赖可以独立运行。它足够简单，可以定制的部分也不少，并且有很多使用的插件。

#### 方案三：使用一个 Panel 和一个 Dock 栏

通常 Panel 也可以作为 Dock 栏使用，但如果你很想再揉进去一个独立的 Dock 也没有问题。

## 决定是否将桌面作为一个目录

要不要在桌面上堆放文件？

### 需要在桌面上堆放文件

如果你喜欢平时把一些文件和目录直接放在桌面上以便操作，就遵照这个部分进行。
下面列出几种选择让你能够在桌面放置文件和目录：

- [PCManFM](https://wiki.archlinux.org/index.php/PCManFM) 是一个文件管理器，不过它有将桌面作为一个目录进行管理的功能。它有 GTK2、GTK3 和 QT 版。但要注意，PCManFM 与 LXPanel 共同依赖于 libfm 库，而 libfm 分为 GTK2 和 GTK3 版本且相互冲突。也就是说，如果你同时使用 LXPanel 与 PCManFM，那么两者必须是同一 GTK 版本的。PCManFM-GTK3 在我这里有拖动文件时不显示的 bug，而 GTK2 毕竟是 10 年前的老设计，个人感觉不太美观，所以最后弃用了这个方案。
- [xfdesktop](https://docs.xfce.org/xfce/xfdesktop/start) 是 XFCE 用来管理桌面的程序，简单易用，但它依赖 [Thunar](https://wiki.archlinux.org/index.php/Thunar) 文件管理器，如果你本身就准备使用 Thunar 的话倒是没什么，但如果你不喜欢 Thunar，xfdesktop 可能不是个好的选择。
- [ROX](http://rox.sourceforge.net/desktop) 其实本身就可以认为是一个桌面环境了，不过它也可以用来管理桌面。我没有用过 ROX，所以就不做更多表述了。

一般来说，用户的“桌面”文件夹在 `~/Desktop`，但修改起来也不难。~~我就是直接将我的 home 目录作为桌面目录的。~~

### 不需要在桌面上堆放文件

如果你为了为了整洁的外观或是其它原因不准备在桌面上放置文件，在这一步你最好开始编辑 Openbox 的右键菜单与壁纸。

#### 编辑 Openbox 的右键菜单

ArchWiki 中有很详细的介绍，可以直接参照[这里](https://wiki.archlinux.org/index.php/openbox#Menus)

#### 设置壁纸

在桌面模式下运行的 PCManFM、xfdesktop 和 ROX 都支持设置壁纸，但在不用它们的情况下，[Nitrogen](https://wiki.archlinux.org/index.php/Nitrogen) 可以用来设置壁纸。

## 让程序自启动

只需要在 `~/.config/openbox/autostart` 或 `~/.xprofile` 加入需要自动执行的命令。两个文件的区别在于，`~/.xprofile` 中的命令是在 X Server 启动时执行的，而 `~/.config/openbox/autostart` 中的内容是在 Openbox 启动后才执行的，一般来说两者区别不大。

一个例子：

    xfdesktop &
    xfce4-panel &
    nm-applet &
    optimus-manager-qt &
    pasystray &
    blueman-applet &
    xfce4-power-manager &
    fcitx5 &

## “设置”程序

Openbox 有自己的设置程序 [ObConf](http://openbox.org/wiki/ObConf:About)，需要单独安装。

[lxappearance](https://wiki.lxde.org/en/LXAppearance) 可以用来设置 GTK 主题等一些选项，虽说从名字里看它是 LXDE 的组件，但它完全可以独立运行。推荐安装它的GTK3版本。

如果你的电脑同时有核显和独显，Arch 系可以用[optimus-manager](https://github.com/Askannz/optimus-manager)配合 GUI 前端 [optimus-manager-qt](https://github.com/Shatur95/optimus-manager-qt) 切换显卡，体验极佳。

## 一些重要的程序

到这里，你的 Openbox 已经达到“可用”状态了，但还需要一些如文件管理器、终端之类的程序。

### 文件管理器

有很多选择，之前提到的 PCManFM、Thunar 都是很不错的轻量级文件管理器，而且在网上随便查查就能找到不少其它的选择。

注意，大多数文件管理器会通过 [gvfs](https://wiki.archlinux.org/index.php/File_manager_functionality) 实现回收站和自动挂载可移动设备的功能，并且可能需要通过一个 daemon 进程监听。

我选择的是 Thunar。

### 终端

之前我们一直临时在用 xterm 作为终端。有很多更好的终端，我自己在用的是 [Sakura](https://launchpad.net/sakura)，非常简洁轻量。[xfce4-terminal](https://docs.xfce.org/apps/terminal/start)、[Termite](https://wiki.archlinux.org/index.php/Termite) 也是不错的选择。

### 文本编辑器

同样有很多选择。单纯作为文本编辑器，[mousepad](https://github.com/codebrainz/mousepad) 很不错。如果你喜欢 Windows 上 Notepad++ 的体验，[notepadqq](https://notepadqq.com) 几乎一模一样，只是现版本的行首缩进貌似有些 bug。
我用的是 Gvim，这个就不用多解释了。

### 用来锁定、退出、关机和重启的面板

我用的是 [Oblogout](https://wiki.archlinux.org/index.php/Oblogout)。

## 系统托盘中的东西

这里列出我使用的组件：

- [nm-applet](https://wiki.archlinux.org/index.php/NetworkManager#nm-applet) 是 NetworkManager 的托盘程序，我还在用 NetworkManager 的原因就是其它网络管理程序没有什么好的 GUI──Wicd 因为使用 Python2 而被 pass 掉，netctl 也没有可以与 nm-applet 媲美的 GUI 前端。
- [blueman-applet](https://wiki.archlinux.org/index.php/Blueman) 配合 blueman-manager 可以很方便地管理蓝牙。想要通过 PulseAudio 使用 APTX 或 LDAC 的话可以使用 [pulseaudio-modules-bt](https://github.com/EHfive/pulseaudio-modules-bt)，Arch 系可以直接通过 AUR 安装，同时要安装 libldac。
- [pasystray](https://github.com/christophgysin/pasystray) 是 PulseAudio 的托盘，功能非常强大，配合 [pavucontrol](https://freedesktop.org/software/pulseaudio/pavucontrol)，操作音频设备就非常容易了。
- [xfce4-power-manager](https://docs.xfce.org/xfce/xfce4-power-manager/start) 是 XFCE 管理电源和屏幕亮度的托盘程序，可以脱离 XFCE 独立安装运行。
- [optimus-manager-qt](https://github.com/Shatur95/optimus-manager-qt) 之前提到过，是 optimus-manager 的 GUI 前端，用来切换显卡。

## 定义键盘快捷键

Openbox的键盘快捷键可以通过编辑 `~/.config/openbox/rc.xml` 修改，当然也有 GUI 前端可以修改快捷键，但是我觉得没有必要，因为 `rc.xml` 修改起来非常简单。

可以参考 [Arch Wiki 中 Openbox 词条的 Keybinds 章节](https://wiki.archlinux.org/index.php/openbox#Keybinds)进行配置。

下面列出我的 `rc.xml` 中的 `<keyboard>` 段，其中包含一些有用的小组件，下文会提到：

    <keyboard>
        <!-- Oblogout 面板 -->
        <keybind key="C-A-Delete">
            <action name="Execute">
                <command>oblogout</command>
            </action>
        </keybind>
    
        <!-- 窗口与工作区相关 -->
        <keybind key="W-a">
            <action name="ToggleShowDesktop"/>
        </keybind>
        <keybind key="A-Left">
            <action name="PreviousWindow"/>
        </keybind>
        <keybind key="A-Right">
            <action name="NextWindow"/>
        </keybind>
        <keybind key="C-Left">
            <action name="DesktopLeft">
                <dialog>no</dialog>
                <wrap>no</wrap>
            </action>
        </keybind>
        <keybind key="C-Right">
            <action name="DesktopRight">
                <dialog>no</dialog>
                <wrap>no</wrap>
            </action>
        </keybind>
        <keybind key="C-A-Left">
            <action name="SendToDesktopLeft">
                <dialog>no</dialog>
                <wrap>no</wrap>
            </action>
        </keybind>
        <keybind key="C-A-Right">
            <action name="SendToDesktopRight">
                <dialog>no</dialog>
                <wrap>no</wrap>
            </action>
        </keybind>
    
        <!-- 用 pactl 控制音量 -->
        <keybind key="XF86AudioMute">
            <action name="Execute">
                <command>pactl set-sink-mute 0 toggle</command>
            </action>
        </keybind>
        <keybind key="XF86AudioRaiseVolume">
            <action name="Execute">
                <command>pactl set-sink-volume @DEFAULT_SINK@ +5%</command>
            </action>
        </keybind>
        <keybind key="XF86AudioLowerVolume">
            <action name="Execute">
                <command>pactl set-sink-volume @DEFAULT_SINK@ -5%</command>
            </action>
        </keybind>
    
        <!-- 用 playerctl 控制上一曲、下一曲、播放与暂停 -->
        <keybind key="XF86AudioPrev">
            <action name="Execute">
                <command>playerctl next</command>
            </action>
        </keybind>
        <keybind key="XF86AudioPlay">
            <action name="Execute">
                <command>playerctl play-pause</command>
            </action>
        </keybind>
        <keybind key="XF86AudioNext">
            <action name="Execute">
                <command>playerctl previous</command>
            </action>
        </keybind>
    
        <!-- 用 escrotum 截图 -->
        <keybind key="Print">
            <action name="Execute">
                <command>escrotum -C</command>
            </action>
        </keybind>
        <keybind key="C-Print">
            <action name="Execute">
                <command>escrotum -Cs</command>
            </action>
        </keybind>
        <keybind key="S-Print">
            <action name="Execute">
                <command>escrotum</command>
            </action>
        </keybind>
        <keybind key="C-S-Print">
            <action name="Execute">
                <command>escrotum -s</command>
            </action>
        </keybind>
    </keyboard>

## 一些好用的程序与小工具

- [escrotum](https://github.com/Roger/escrotum) 是一个非常轻量的截图工具。
- [DeaDBeeF](https://deadbeef.sourceforge.io) 神似 foobar2000，很轻量好用的音乐播放器，可惜用的不是 MPRIS D-Bus Interface Specification 标准，不过可以在应用内设置快捷键达到一样的效果。
- [playerctl](https://github.com/altdesktop/playerctl) 可以控制使用了 [MPRIS D-Bus Interface Specification](https://specifications.freedesktop.org/mpris-spec) 标准的播放器进行上一曲、下一曲、播放与暂停操作。
- [GPicView](https://wiki.lxde.org/en/GPicView) 是 LXDE 默认的图片查看器，轻量好用。有 GTK2 和 GTK3 版本，一般用 GTK3 版本就可以了。
- [Engrampa](https://github.com/mate-desktop/engrampa) 是 MATE 默认的压缩文件管理器，个人认为 Linux 下最好的 GUI 压缩文件管理器。
- [Parcellite](http://parcellite.sourceforge.net) 是一个轻量的剪切板管理器，简单够用。

## 关于 compositor

首先你需要问问自己，你是否真的需要 compositor？
诚然，compositor 可以装饰窗口、实现透明效果，Compiz 这样重量级的 compositor 甚至可以实现很多炫酷的特效。但这值得吗？这些效果无法带来实质上的效率提升。compositor 在后台会占用很多 GPU 资源从而导致帧率的下降，如果你是游戏玩家的话这尤为关键：在我的电脑上，开启 compositor 后 CS:GO 的帧率会下降接近四分之一。
另外，没有正确配置的 compositor 可能会导致令人很不舒服的画面撕裂等问题。

如果你一定要使用 compositor，我推荐以下两个：

- [picom](https://wiki.archlinux.org/index.php/Picom) 是已经停止开发的 Compton 的后继开发产物，简单轻量，适合不需要太多装饰与特效的人。
- [Compiz](http://www.compiz.org) 是最有名的重量级 compositor，它甚至可以单独作为一个 WM 使用。能实现非常多效果，并且有完备的生态与社区。

## 对触控板的调校

可以参考我的另一篇文章[《对 Linux 下触控板按键、加速和手势的优化（libinput）》](https://www.eaimty.com/2020/09/optimize-touchpad-on-linux-with-libinput-driver.html)

# 写作初衷

去年我买了一台游戏本作为主力 PC，当时觉得，既然是游戏本，那用 Windows 应该是理所当然的，就把我的整个工作环境迁移到了 Windows 上。用了大概半年，我彻底受够了 Windows：催命般的 Windows Update、相当于没有的包管理...最恶心的还是注册表，这东西简直就是人类科技的倒退。

今年一月，我切换到了最近大红大紫的 Manjaro，具体版本是 Manjaro XFCE Edition Minimal。
这一用就是半年，我最终还是决定换掉它，原因就是：它注重“傻瓜化”。但我不是傻瓜啊...Manjaro 为了达到“开箱即用”的状态集成了太多没用的组件，就算是 Minimal 版本也是如此。例如，yay 已经够好了，然而 Manjaro 还是要再带一个 pamac，虽然说 pamac 的重点在于它的 GUI,但市面上 pacman 和 AUR Helper 的 GUI 已经很多了，使用体验不错的也有不少。另外，不知为何，Manjaro 经常会在更新时搞坏一些东西，默认的 theme 就在某次大版本更新时坏掉过，导致很多 GTK 程序变得辣眼睛；Optimus Manager 也在某次更新过后出过问题，需要重启 2 次 DE 才能成功切换显卡。每次处理这类问题都十分麻烦耗时，这也是庞大臃肿系统桌面环境的通病。
我对 Manjaro 的最终印象是：适合小白与非常懒的用户（连手动配置一次环境都懒得做的那类人），且由于 Manjaro 基于 Arch，有 AUR 这样一个方便的软件包来源，几乎没有手动编译软件的需要。

最终我还是换回了 Arch，自己动手拼凑了桌面环境。
感觉在中文互联网上关于自己拼凑 Linux 桌面环境的文章少之又少，于是便有了写这篇文章的想法。在鸽了 N 个星期后，终于写完并发布了这篇文章。
