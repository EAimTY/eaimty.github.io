+++
title = "在 Android ROM 包中加入 BusyBox"
date = 2016-02-10

[extra.comments]
issue_id = 8
+++

> BusyBox是一个遵循GPL协议、以自由软件形式发行的应用程序。Busybox在单一的可执行文件中提供了精简的Unix工具集，可运行于多款POSIX环境的操作系统，例如Linux（包括Android）、Hurd、FreeBSD等等。由于BusyBox可执行文件尺寸小、并通常使用 Linux内核，这使得它非常适合使用于嵌入式系统。此外，由于BusyBox功能强大，因此有些人将 BusyBox 称为“嵌入式Linux的瑞士军刀”。
——维基百科

其实 Android 已经内建了一种命令行工具 ToolBox，但实际的操作体验...😂

<!--more-->

首先，到 <https://www.busybox.net> 下载最新的 BusyBox 二进制文件。

然后，将 zip 格式的 ROM 包解包，将下载到的 BusyBox 二进制文件重命名为 `busybox`，放置在 `/system/xbin` 目录下（放在别的地方也没问题，但是要注意替换后面脚本中的路径）。

找到刷机脚本文件，通常是 `/META-INF/com/google/android/updater-script`，在 `unmount("/system");` 之前插入以下内容：

```sh
run_program(
    "/system/xbin/busybox",
    "--install",
    "-s",
    "/system/xbin"
);

symlink(
    "busybox",
    "/system/xbin/[",
    "/system/xbin/[[",
    "/system/xbin/ash",
    "/system/xbin/awk",
    "/system/xbin/basename",
    "/system/xbin/bunzip2",
    "/system/xbin/bzip2",
    "/system/xbin/cal",
    "/system/xbin/cat",
    "/system/xbin/chgrp",
    "/system/xbin/chmod",
    "/system/xbin/chown",
    "/system/xbin/cmp",
    "/system/xbin/cp",
    "/system/xbin/cpio",
    "/system/xbin/cut",
    "/system/xbin/date",
    "/system/xbin/dd",
    "/system/xbin/df",
    "/system/xbin/diff",
    "/system/xbin/dos2unix",
    "/system/xbin/du",
    "/system/xbin/echo",
    "/system/xbin/egrep",
    "/system/xbin/expr",
    "/system/xbin/false",
    "/system/xbin/fgrep",
    "/system/xbin/find",
    "/system/xbin/free",
    "/system/xbin/ftpget",
    "/system/xbin/ftpput",
    "/system/xbin/grep",
    "/system/xbin/gunzip",
    "/system/xbin/gzip",
    "/system/xbin/ifconfig",
    "/system/xbin/insmod",
    "/system/xbin/kill",
    "/system/xbin/killall",
    "/system/xbin/less",
    "/system/xbin/lsmod",
    "/system/xbin/md5sum",
    "/system/xbin/mknod",
    "/system/xbin/more",
    "/system/xbin/netstat",
    "/system/xbin/od",
    "/system/xbin/pidof",
    "/system/xbin/ping",
    "/system/xbin/ping6",
    "/system/xbin/printf",
    "/system/xbin/ps",
    "/system/xbin/rm",
    "/system/xbin/rmmod",
    "/system/xbin/route",
    "/system/xbin/sed",
    "/system/xbin/seq",
    "/system/xbin/sort",
    "/system/xbin/strings",
    "/system/xbin/tail",
    "/system/xbin/telnet",
    "/system/xbin/test",
    "/system/xbin/tftp",
    "/system/xbin/top",
    "/system/xbin/touch",
    "/system/xbin/true",
    "/system/xbin/uname",
    "/system/xbin/unix2dos",
    "/system/xbin/unzip",
    "/system/xbin/vi",
    "/system/xbin/wc",
    "/system/xbin/wget",
    "/system/xbin/which",
    "/system/xbin/xargs",
    "/system/xbin/yes"
    #这里添加其它链接
);
```

当然，BusyBox 还有很多其它玩法，将链接加到脚本里即可。

这次偷个懒，就写这么多了😜
