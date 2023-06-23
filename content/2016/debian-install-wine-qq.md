+++
title = "在 Debian 下安装 wineQQ"
date = 2016-01-19

[extra.comments]
issue_id = 5

[[extra.comments.addition]]
user = { login = "bigmercu", avatar_url = "https://secure.gravatar.com/avatar/87e44ae39d5fcaed9c5af1a33d52c16b?d=retro" }
created_at = "2016-05-14T07:19:42Z"
body = "不错哦  试试"
+++

最近几年，微软把 Windows 做得越来越渣。相比之下，桌面 Linux 的势头越来越猛，很多用户看中了 Linux 的安全性、开源性与稳定性，便也踏上了这条大船。

可很多新的 Linuxer 在第一次进入桌面时便傻了眼：Linux 下怎么用软件啊？很多“必备软件”为什么在 Linux 下没有啊...
当然，在一段时间的使用之后，用户们就都会自己用 apt、yum 之类的管理工具安装软件包了。但是，还是有一些软件仅能在 Windows 下运行。假如用户既不想装双系统，又不想开虚拟机，那怎么办呢？两全其美的办法就是——使用 wine。

<!--more-->

**2016 年 6 月 15 日注：已更新为 wine1.9 的使用步骤**

来看看[中文维基百科](https://zh.wikipedia.org/wiki/Wine)上关于 Wine 的介绍：

> Wine是一个在x86、x86-64上允许类Unix操作系统在X Window System下运行Microsoft Windows程序的软件。另一方面，计算机程序设计师能经由Wine的程序库将视窗的程序转移至类Unix操作系统中运行。
Wine不是Windows模拟器，而是运用API转换技术实做出Linux对应到Windows相对应的函数来调用DLL以运行Windows程序。Wine是自由软件，在GNU宽通用公共许可证（LGPL）下发布。
  
Wine 不是虚拟机，而类似于 Windows Subsystem Linux 的实现方法，它是一个用来将 Windows 专用的函数转换为 Linux、macOS、Unix、BSD 等系统相对应的函数的程序。

下面以 Debian（Gnome 环境）下 wineQQ 的安装为例介绍一下 wine 的使用方法

# 安装 wine
最新的 wine 已经升级到了 1.9，兼容性有了很大的提升，但 Debian 的仓库中默认是 wine1.6。在 [WineHQ 官网](https://www.winehq.org/)发现有 Debian 的软件源，所以我们要先添加官方的源。

由于 wine 只提供 32 位版本，64 位系统需要打开 32 位软件的支持：

`$ sudo dpkg --add-architecture i386`

编辑 `/etc/apt/sources.list` 添加源信息：

`$ sudo vim /etc/apt/sources.list`

在弹出文件的末尾加上：

`deb https://dl.winehq.org/wine-builds/debian/ stable main`

这里需要注意，apt 需要 `apt-transport-https` 这个包才可以正常获取到 HTTPS 协议的库。

然后导入公钥：

`$ wget https://dl.winehq.org/wine-builds/Release.key`

`$ sudo apt-key add Release.key`

更新 apt 软件包列表：

`$ sudo apt-get update`

开始安装！

`$ sudo apt-get install winehq-devel`

（经过漫长的等待，终于把所有软件包都装完了。如果下载速度比较慢，推荐使用 [apt-fast](https://github.com/ilikenwf/apt-fast) 安装。）

# 安装 wine 自定义工具 winetricks

winetricks 是一个用户友好的用来配置 wine 容器的工具。

[Debian 中文软件仓库](https://repo.debiancn.org/) 中有 winetricks 这个包，添加过这个源的同学可以直接用 apt 安装。

如果你不想添加这个源，也可以直接去搞到可执行文件，首先下载：

`$ wget https://raw.githubusercontent.com/Winetricks/winetricks/master/src/winetricks`

给它执行权限：

`$ chmod +x winetricks`

把它移动到 `/usr/local/bin` 下：

`$ sudo mv winetricks /usr/local/bin`

# 配置 wine 环境

首先，假如你的 Linux 是 64 位的，就需要先将 wine 设置为使用 32 位环境：

`$ export WINEARCH=win32`

为了让 QQ 能跑起来，需要安装一些 wine 组件：

`$ winetricks -q riched20 ie6 mfc42`

另外，为了使 QQ 在打开链接时 winebrowser 不报错，需要设置使用 wine 内建的 `urlmon.dll`

`$ winecfg`

在`函数库`选项卡中的`已有的函数库顶替`中找到 `urlmon.dll`（若没有则先通过“新增函数库顶替”添加），点击`编辑`，选择载入顺序为`内建`。

![使用内建的 urlmon.dll](/pictures/574649ac2861e.png)

解决蜜汁中文字体：

`$ winetricks fakechinese`

# 安装并配置QQ

终于到了重头戏了，首先下载 QQ 的安装包，这里推荐 [QQ 轻聊版](http://im.qq.com/lightqq/)。

下载后使用 Wine 打开，照常安装，我这里安装的很快，只是在注册组件那一步等了半天。
安装后，我满怀喜悦地准备打开 QQ，但直接弹出`安全组件错误`...
由于疼逊的安全组件无法在 wine 下正常运行，导致 QQ 无法启动。这让我头疼了很长一阵子...在翻阅了无数网页后，我终于找到了解决方法：

[腾讯QQ7.x 去整体安全校验补丁](http://www.zdfans.com/589.html)

按照说明将 QQ 的流氓组件砍掉后，QQ 正常启动了，还剩下一个问题没有解决，那就是程序的启动快捷方式。
安装下面的内容写一个 QQ.desktop，放在 `~/.local/share/applications/` 下

```conf
[Desktop Entry]
Categories=Network
Exec=wine "～/.wine/drive_c/Program Files/Tencent/QQLite/Bin/QQ.exe" '这里是QQ.exe的路径
Icon=～/.wine/drive_c/Program Files/Tencent/QQLite/Bin/qq.png '这里是图标的路径
Name=QQ
NoDisplay=false
StartupNotify=true
Terminal=false
Type=Application
Name[zh_CN]=QQ
```

到这里，Wine QQ 就安装完成了。
