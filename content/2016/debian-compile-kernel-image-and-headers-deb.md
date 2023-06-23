+++
title = "Debian 下编译 linux-image 与 linux-headers 的 DEB 包"
date = 2016-06-20

[extra.comments]
issue_id = 15

[[extra.comments.addition]]
user = { login = "nrechn", html_url = "https://gyz.io", avatar_url = "https://secure.gravatar.com/avatar/9bcc01c06b1a3ccb58442236364e67c4?d=retro" }
created_at = "2016-07-14T23:32:05Z"
body = "内核配置怎么可以默认的就行？#(滑稽)，难怪泥会编译一个多小时呢。而且默认的居然能用起来？？反正我默认的话根本启动不了系统。\n\n表示从来都是自己修改内核配置，刚开始要搞4个小时配置内核，熟悉之后直接沿用原先的config，重新选的时候凭记忆大概10来分钟能几乎把所有选项过一遍。然后双核i5十分钟编译好内核#(滑稽)\n\n另外实际上没必要这么麻烦啦，只要把linux-headers包打开来看看 header files 丢哪儿了，然后把编译内核的临时目录link过去即可，打包什么的多麻烦 #(滑稽)"

[[extra.comments.addition]]
user = { login = "frytea", html_url = "https://www.frytea.com", avatar_url = "https://secure.gravatar.com/avatar/efe8762bf0cd029d0a6d24e0faf47adf?d=retro" }
created_at = "2021-11-15T02:46:45Z"
body = "`sudo cat debian.iso > /dev/sdb` 这个真是学到了，改日试试"

[[extra.comments.addition]]
user = { login = "四斋破乙poi", avatar_url = "https://secure.gravatar.com/avatar/a0b790504103d79d05eda548601ca033?d=retro" }
created_at = "2017-03-18T06:20:06Z"
body = "我只是从基佬云音乐过来逛逛的"

[[extra.comments.addition]]
user = { login = "EAimTY", html_url = "https://www.eaimty.com/", avatar_url = "https://secure.gravatar.com/avatar/f64037e002ad7b01fe9ee60351bfd1d2?d=retro" }
created_at = "2016-07-15T14:35:18Z"
body = "> 内核配置怎么可以默认的就行？#(滑稽)，难怪泥会编译一个多小时呢。而且默认的居然能用起来？？反正我默认的话根本启动不了系统。\n> \n> 表示从来都是自己修改内核配置，刚开始要搞4个小时配置内核，熟悉之后直接沿用原先的config，重新选的时候凭记忆大概10来分钟能几乎把所有选项过一遍。然后双核i5十分钟编译好内核#(滑稽)\n> \n> 另外实际上没必要这么麻烦啦，只要把linux-headers包打开来看看 header files 丢哪儿了，然后把编译内核的临时目录link过去即可，打包什么的多麻烦 #(滑稽)\n\n的确能用，但问题的确很多...😅不过最近以换Arch了，就不存在这种问题了😂另外，我的i5可是睾贵的第一代i5-560M，能流畅运行扫雷😂"
+++

**2018年2月2日注：其实升级 Debian 内核的最好方法，就是换用 Debian sid...省去了很多麻烦，况且 sid 在稳定性上也不算差。**

最近用了几天 win10，结果被恶心到了。具体就不说了，总之就是各种各样的垃圾、各种各样的不和谐。

惹不起惹不起，我还是滚回 Linux 吧。

重新回到熟悉的 Debian+Gnome 环境，突发奇想准备升级一下内核。于是折腾了半天，把内核升级到了 4.6.2。
或许是 Debian 8 的默认内核太古老（3.x）（CentOS 6 表示不服），也可能是心理作用，感觉 4.x 运行起来的确比 3.x 快了不少，于是手贱直接把旧内核 remove 掉了，而且还加了 purge...(+_+)

但没过多长时间，就发现了问题：VirtualBox 无法启动虚拟机...查了一下 log，发现原来是 VirtualBox Kernel Modules 出了问题：
VirtualBox Kernel Modules 需要通过 DKMS 使用 linux-headers，而我已经把旧版本的 linux-image 和 linux-headers 都 purge 掉了。然而通过 apt，无论用 testing 源还是 sid(unstable) 源都无法直接安装对应新版本（也就是我装的 4.6.2 版）的 linux-headers 包。

~~蛋碎一地...Σ(ﾟДﾟ)~~

通过 Google 到的方法，也就是使用 `make headers_install`，DKMS 仍然报错...
没办法了，重装 Debian...

<!--more-->

顺便发现了一个制作 Debian 安装 U 盘的好方法方法，只需两条命令即可：

`$ sudo cat debian.iso > /dev/sdb`

`$ sudo sync`

把这里的 `debian.iso` 换成安装镜像的名称，保证 `/dev/sdb` 是你的U盘

新系统装好后没多长时间，手贱又发作了，又把 Kernel 升级到了 4.6.2（我真是没救了）。不过这次吸取了上次的教训，折腾了半天，找到一种直接把 Kernel 源码编译成 linux-image 和 linux-headers 的 DEB 包的方法，使 DKMS 可以正常编译 VirtualBox Kernel Modules。~~这回能好好玩了~~

首先安装所需的工具：

`$ sudo apt-get install git fakeroot build-essential ncurses-dev xz-utils libssl-dev bc kernel-package`

需要下载 1GB 左右的文件，耐心等吧。

下一步当然是去 [kernel.org](https://kernel.org/) 下载内核源码，推荐下载最新的 stable 版，下载 .tar.xz 格式的包即可。
当然，国内连接 kernel.org 的速度不敢恭维，可以到[中科大的源](https://mirrors.ustc.edu.cn/kernel.org/linux/kernel/v4.x/)下载。

然后解压：

`$ xz -d ***.tar.xz`

`$ tar -xvf ***.tar`

通过上面的 xz 命令可以把 .tar.xz 解包成常见的 .tar，然后按常规方式解压即可。
之后 cd 进解压后的目录，转到 root 用户
可以按照你的需求改动内核文件。

清理一下之前编译的文件：

`# make mrproper`

生成 .config 文件：

`# make menuconfig`

设置好选项，最后选 save。

清理一下：

`# make-kpkg clean`

下面开始编译 deb：

`# fakeroot make-kpkg --initrd --revision=4.6.2-EAimTY kernel_image kernel_headers -j2`

在这句命令中：
`--initrd` 表示创建 initrd；
`--revision=4.6.2-EAimTY` 中的 `4.6.2-EAimTY` 是创建的 deb 包的版本号，可以自行修改；
`kernel_image` 表示编译 linux-image; `kernel_headers` 表示编译 linux-headers，如果不需要可以去掉；
`-j2` 表示使用两个 CPU 核心，具体视 CPU 参数而定，举个栗子：假如你用的 CPU 是最新的 Intel Xeon E5 2699 v4，也就是说有 22 个核心，就把这个参数换成 `-j22`（不过貌似这个参数最多支持到 `-j9`...）
想知道你的 CPU 有几个核心，可以使用 `$ cat /proc/cpuinfo | grep "cpu cores"` 查看

经过漫长的等待（我双核 i5，4G 内存的笔记本花了一个多小时），linux-image 和 linux-header 的 deb 包就会出现在上层目录中了。用 `dpkg -i` 安装。

重启后，通过 GRUB 高级选项中就会出现新的内核，进入后，如果有强迫症，可以卸装掉旧的内核：

`$ sudo dpkg -l "linux-image*"`

`$ sudo dpkg -l "linux-headers*"`

两条命令会分别列出已安装的 linux-image 和 linux-headers，可以用 apt 删掉。

通过以上方法安装 linux-image 和 linux-headers 后，VirtualBox 仍然可以完美运行，只需要用 DKMS 重新生成一下 VirtualBox Kernel Modules 即可。
