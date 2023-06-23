+++
title = "CentOS 7 下搭建 Shadowsocks 服务器"
date = 2016-02-05

[extra.comments]
issue_id = 7

[[extra.comments.addition]]
user = { login = "amen", avatar_url = "https://secure.gravatar.com/avatar/da30425b4b8732a6caeefb5d45fb8b46?d=retro" }
created_at = "2019-06-29T18:49:58Z"
body = "在别的大佬那里看到说unit那里加After=network.target会比较好0.0\n\n[Unit]\nDescription=Shadowsocks Client Service\nAfter=network.target\n\n[Service]\nType=simple\nUser=root\nExecStart=/usr/bin/sslocal -c /home/xx/Software/ShadowsocksConfig/shadowsocks.json\n\n[Install]\nWantedBy=multi-user.target"

[[extra.comments.addition]]
user = { login = "EAimTY", html_url = "https://www.eaimty.com/", avatar_url = "https://secure.gravatar.com/avatar/f64037e002ad7b01fe9ee60351bfd1d2?d=retro" }
created_at = "2018-03-01T11:56:59Z"
body = "> [Service]\n> TimeoutStartSec=0\n> ExecStart=/usr/bin/ssserver -c /etc/shadowsocks.json\n> \n> 这段只能用systemctl enable shadowsocks吧。\n> systemctl start shadowsocks\n> 报错：Unit etc-systemd-system-shadowsocks.service.mount could not be found.\n\n额...你这明显是service名错了😅"

[[extra.comments.addition]]
user = { login = "liwl", html_url = "https://www.1iwl.com", avatar_url = "https://secure.gravatar.com/avatar/113b90ef57818573293f2eb3216b5951?d=retro" }
created_at = "2018-02-28T07:17:46Z"
body = "[Service]\nTimeoutStartSec=0\nExecStart=/usr/bin/ssserver -c /etc/shadowsocks.json\n\n这段只能用systemctl enable shadowsocks吧。\nsystemctl start shadowsocks\n报错：Unit etc-systemd-system-shadowsocks.service.mount could not be found."

[[extra.comments.addition]]
user = { login = "歪柠", avatar_url = "https://secure.gravatar.com/avatar/bdc22da228fe088bc5c4f7a0932f0342?d=retro" }
created_at = "2021-03-01T07:32:34Z"
body = "haha备案信息"
+++

**本文更新于 2017 年 11 月 10 日，0x13 大之后的几日**

从 2017 年秋季开始，墙的升级速度不断变快，同时还动用了不少物理手段，约谈了一些相关人士。
上次更新本文已经是一年半之前了，其中用 PPTP 翻越长城的方法已经彻底挂掉。之前很多常用的 ss 加密算法也变得不再安全，所以是时候更新一下文章了。

<!--more-->

# 关于系统

除非有特殊需要，建议使用的所有软件、系统的最新稳定版本并持续更新，来保证安全性。
这里选用 CentOS 7 作为系统。

如果你的机器是 KVM 或其它支持更换内核的架构，强烈建议把内核升级到 4.9 以上的版本，并开启 [tcp bbr](https://github.com/google/bbr) 拥堵控制算法，在多数网络情况下（特别是丢包率高时）可以大幅度提升连接速度和稳定性。开启 bbr 算法的方式可以参考[这篇文章](https://www.isthnew.com/centos7-bbr/)。

# 安装软件包

这里我们选择 Shadowsocks 最正统的 Python 分支。
首先需要安装 pip，但 CentOS 官方的 yum 源中并没有 pip，所以我们需要先安装 EPEL 源：

`# yum install epel-release`

待安装完后，我们便可以安装 pip：

`# yum install python-pip`

虽然安装好了 pip，但 pip 源中的 Shadowsocks 的版本已经非常老了，查阅 Shadowsocks Wiki 后找到了正确的安装姿势：

`# pip install git+https://github.com/shadowsocks/shadowsocks.git@master`

Shadowsocks 可以使用多种加密算法，而现在安全性比较强的算法基本上都需要 libsodium 库：

`# yum install libsodium`

安装完成。

# 配置 Shadowsocks

这一步十分简单，因为我们使用的不是 Shadowsocks 多用户版，所以只需要一行命令即可解决：

`# ssserver -p 端口号 -k 密码 -m 加密方式 -d start`

在这里，端口号推荐使用最常用的几个（例如 443、8080 等）防止被防火墙特殊照顾，加密方式推荐 chacha20-ietf-poly1305，大多数其它算法现在都已经变得不安全了。

举个栗子，假如我们使用 443 端口，加密方式选 rc4-md5，就执行：

`# ssserver -p 443 -k 密码 -m rc4-md5 -d start`

但这样启动会出现一些问题，因为在 SSH 连接中断后，在这个连接内启动的进程也会被杀掉。
可以把 ss 的进程放进一个专用的 screen 中来解决这个问题。

首先我们需要 screen 这个软件包：

`# yum install screen`

然后新建一个名字叫 shadowsocks 的 screen：

`# screen -S shadowsocks`

进入新的 screen 后，我们可以按正常方式启动 shadowsocks，然后按 Ctrl+A 再按 D 键把 screen 放进后台。
这样 ss 就在后台跑起来了。
（想要重新调出这个 screen 窗口，执行 `# screen -r shadowsocks` 即可）

# 开机自启

CentOS 7 最大的变化应该就是把 init.d 换成了 systemd，所以不太推荐再使用 rc.local 脚本的方式让 ss 开机自启，正确姿势应该是用 systemctl 控制启动。

首先我们可以新建一个 ss 的配置文件已便于之后的操作，新建一个 `/etc/shadowsocks.json`，内容如下：

```json
{
  "server": "0.0.0.0",
  "port_password": {
    "端口 1": "端口 1 密码",
    "端口 2": "端口 2 密码",
    "端口 3": "端口 3 密码"
  },
  "method": "chacha20-ietf-poly1305"
}
```

这是最简单的多用户配置文件，如果需要更多用户，就添加更多行，如果不需要多用户，把多余的两行删掉即可。
（注意除最后一行外每一行末必须要加英文的半角逗号）

之后我们新建一个 shadowsocks 的 systemd 配置文件，文件名为 `shadowsocks.service`，内容如下：

```conf
[Unit]
Description=Shadowsocks

[Service]
TimeoutStartSec=0
ExecStart=/usr/bin/ssserver -c /etc/shadowsocks.json

[Install]
WantedBy=multi-user.target
```

把这个文件放进 `/etc/systemd/system/` 目录下，给它运行权限（`# chmod +x /etc/systemd/system/shadowsocks.service`）

把它设为开机启动项：

`# systemctl enable shadowsocks`

这样 shadowsocks 就可以开机自动运行了。
如果不放心可以先通过 systemd 启动 shadowsocks 测试一下：

`# systemctl start shadowsocks`

查看一下服务状态：

`# systemctl status shadowsocks`

如果显示没有问题，就用你的 shadowsocks 客户端尝试连接一下，能连上的话就说明搭建成功了。

# 写在最后

在 10 月底，ss 的流量特征已经能靠机器学习被检测到了，有关论文在[这里](http://ieeexplore.ieee.org/document/8048116/)，中文翻译版本在[这里](https://juejin.im/post/59dfbd93f265da430d570482)（请只关注这篇论文的技术部分）。近期 GFW 的动作是只封端口，遇到大流量且无法识别协议的情况就会封掉这个端口，解决方式只有换成常用端口或直接更换 IP。

**纸是包不住火的，不论他们怎么建墙掩人耳目，到头来都会是自掘坟墓。**

最后用 Shadowsocks 原作者 Clowwindy 的一句话结束：

**I hope one day I’ll live in a country where I have freedom to write any code I like without fearing.**
