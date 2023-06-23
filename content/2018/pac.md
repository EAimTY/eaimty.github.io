+++
title = "GFWList AutoProxy PAC File"
date = 2018-05-23

[extra.comments]
issue_id = 28

[[extra.comments.addition]]
user = { login = "老大哥", html_url = "http://www.gov.cn", avatar_url = "https://secure.gravatar.com/avatar/3232a9185d67f65bcb803bf4f9013d77?d=retro" }
created_at = "2018-11-21T06:51:08Z"
body = "老大哥一直在看着你"

[[extra.comments.addition]]
user = { login = "Woody", avatar_url = "https://secure.gravatar.com/avatar/ce8649da130d6b315e5436f10285ba26?d=retro" }
created_at = "2020-02-05T04:47:48Z"
body = "> > > > 你好\n> > > \n> > > 我关闭 https://pac.eaimty.com 的一个原因就是其已在国内被限制访问了，而且我的PAC文件也是用genpac生成的，所以你遇到的问题应该不是PAC文件有问题。\n> > > 是不是用genpac的时候写错了代理类型（是不是误写了HTTP？）或端口？\n> > > 我没用过Ubuntu所以不太清楚“network proxy模式”，但是我在Debian下使用放在本地的PAC文件时也遇到过类似情况：\n> > > 在`/etc/enviroment`中加入了：\n> > > \n> > >     auto_proxy=\"file:///PATH/TO/PAC\"\n> > >     AUTO_PROXY=\"file:///PATH/TO/PAC\"\n> > > \n> > > 代理却没有生效。\n> > > \n> > > 我的解决方法是在本机上装个Nginx，用它listen一个端口serve本机上的PAC文件，并且设置开机自启，配置文件的server段如下：\n> > > \n> > >     server {\n> > >         listen       1081;\n> > >         server_name  localhost;\n> > >         location / {\n> > >             root      /PAC_FILE_DIRECTORY/;\n> > >             try_files /PAC_FILE /PAC_FILE;\n> > >         }\n> > >     }\n> > > \n> > > 然后在`/etc/enviroment`中加入：\n> > > \n> > >     auto_proxy=\"http://127.0.0.1:1081\"\n> > >     AUTO_PROXY=\"http://127.0.0.1:1081\"\n> > > \n> > > 之后PAC规则就生效了。\n> > > 希望对你有帮助。\n> > \n> > 你好，\n> > 能否把你的pac文件发一份到我的邮箱woody00h@gmail.com.我一试便知是不是pac的问题。谢谢！\n> \n> 我现在使用的是这个白名单 https://github.com/MatcherAny/whitelist.pac 原封不动没有做任何更改\n\n你好"

[[extra.comments.addition]]
user = { login = "Woody", avatar_url = "https://secure.gravatar.com/avatar/ce8649da130d6b315e5436f10285ba26?d=retro" }
created_at = "2020-02-04T09:11:36Z"
body = "> > 你好\n> \n> 我关闭 https://pac.eaimty.com 的一个原因就是其已在国内被限制访问了，而且我的PAC文件也是用genpac生成的，所以你遇到的问题应该不是PAC文件有问题。\n> 是不是用genpac的时候写错了代理类型（是不是误写了HTTP？）或端口？\n> 我没用过Ubuntu所以不太清楚“network proxy模式”，但是我在Debian下使用放在本地的PAC文件时也遇到过类似情况：\n> 在`/etc/enviroment`中加入了：\n> \n>     auto_proxy=\"file:///PATH/TO/PAC\"\n>     AUTO_PROXY=\"file:///PATH/TO/PAC\"\n> \n> 代理却没有生效。\n> \n> 我的解决方法是在本机上装个Nginx，用它listen一个端口serve本机上的PAC文件，并且设置开机自启，配置文件的server段如下：\n> \n>     server {\n>         listen       1081;\n>         server_name  localhost;\n>         location / {\n>             root      /PAC_FILE_DIRECTORY/;\n>             try_files /PAC_FILE /PAC_FILE;\n>         }\n>     }\n> \n> 然后在`/etc/enviroment`中加入：\n> \n>     auto_proxy=\"http://127.0.0.1:1081\"\n>     AUTO_PROXY=\"http://127.0.0.1:1081\"\n> \n> 之后PAC规则就生效了。\n> 希望对你有帮助。\n\n你好，\n能否把你的pac文件发一份到我的邮箱woody00h@gmail.com.我一试便知是不是pac的问题。谢谢！"

[[extra.comments.addition]]
user = { login = "Woody", avatar_url = "https://secure.gravatar.com/avatar/ce8649da130d6b315e5436f10285ba26?d=retro" }
created_at = "2020-02-03T09:19:23Z"
body = "你好"

[[extra.comments.addition]]
user = { login = "游客", avatar_url = "https://secure.gravatar.com/avatar/?d=retro" }
created_at = "2019-09-09T01:29:39Z"
body = "感谢更新，在火狐上可以用，省去一个扩展"

[[extra.comments.addition]]
user = { login = "EAimTY", html_url = "https://www.eaimty.com/", avatar_url = "https://secure.gravatar.com/avatar/f64037e002ad7b01fe9ee60351bfd1d2?d=retro" }
created_at = "2020-02-04T09:16:27Z"
body = "> > > 你好\n> > \n> > 我关闭 https://pac.eaimty.com 的一个原因就是其已在国内被限制访问了，而且我的PAC文件也是用genpac生成的，所以你遇到的问题应该不是PAC文件有问题。\n> > 是不是用genpac的时候写错了代理类型（是不是误写了HTTP？）或端口？\n> > 我没用过Ubuntu所以不太清楚“network proxy模式”，但是我在Debian下使用放在本地的PAC文件时也遇到过类似情况：\n> > 在`/etc/enviroment`中加入了：\n> > \n> >     auto_proxy=\"file:///PATH/TO/PAC\"\n> >     AUTO_PROXY=\"file:///PATH/TO/PAC\"\n> > \n> > 代理却没有生效。\n> > \n> > 我的解决方法是在本机上装个Nginx，用它listen一个端口serve本机上的PAC文件，并且设置开机自启，配置文件的server段如下：\n> > \n> >     server {\n> >         listen       1081;\n> >         server_name  localhost;\n> >         location / {\n> >             root      /PAC_FILE_DIRECTORY/;\n> >             try_files /PAC_FILE /PAC_FILE;\n> >         }\n> >     }\n> > \n> > 然后在`/etc/enviroment`中加入：\n> > \n> >     auto_proxy=\"http://127.0.0.1:1081\"\n> >     AUTO_PROXY=\"http://127.0.0.1:1081\"\n> > \n> > 之后PAC规则就生效了。\n> > 希望对你有帮助。\n> \n> 你好，\n> 能否把你的pac文件发一份到我的邮箱woody00h@gmail.com.我一试便知是不是pac的问题。谢谢！\n\n我现在使用的是这个白名单 https://github.com/MatcherAny/whitelist.pac 原封不动没有做任何更改"

[[extra.comments.addition]]
user = { login = "EAimTY", html_url = "https://www.eaimty.com/", avatar_url = "https://secure.gravatar.com/avatar/f64037e002ad7b01fe9ee60351bfd1d2?d=retro" }
created_at = "2020-02-04T05:24:04Z"
body = "> 你好\n\n我关闭 https://pac.eaimty.com 的一个原因就是其已在国内被限制访问了，而且我的PAC文件也是用genpac生成的，所以你遇到的问题应该不是PAC文件有问题。\n是不是用genpac的时候写错了代理类型（是不是误写了HTTP？）或端口？\n我没用过Ubuntu所以不太清楚“network proxy模式”，但是我在Debian下使用放在本地的PAC文件时也遇到过类似情况：\n在`/etc/enviroment`中加入了：\n\n    auto_proxy=\"file:///PATH/TO/PAC\"\n    AUTO_PROXY=\"file:///PATH/TO/PAC\"\n\n代理却没有生效。\n\n我的解决方法是在本机上装个Nginx，用它listen一个端口serve本机上的PAC文件，并且设置开机自启，配置文件的server段如下：\n\n    server {\n        listen       1081;\n        server_name  localhost;\n        location / {\n            root      /PAC_FILE_DIRECTORY/;\n            try_files /PAC_FILE /PAC_FILE;\n        }\n    }\n\n然后在`/etc/enviroment`中加入：\n\n    auto_proxy=\"http://127.0.0.1:1081\"\n    AUTO_PROXY=\"http://127.0.0.1:1081\"\n\n之后PAC规则就生效了。\n希望对你有帮助。"
+++

**由于GFWList已经无法囊括越来越多的被墙站点，白名单是更好的选择**
**况且我的服务器不在你国境内，通信干扰严重，所以该服务已经关闭**

最近重新开始用 Shadowsocks-Qt5，但因为 ss-qt5 不能在应用内直接获取 PAC，所以我把之前上过线但已经被我下线的 [GFWList AutoProxy PAC File](https://pac.eaimty.com) 重新搞了出来：

<https://pac.eaimty.com>

使用 genpac 生成，每天更新的 GFWList PAC 文件
文件中默认的 proxy 是 SOCKS5, 127.0.0.1:1080

对于 Linux，编辑 `/etc/enviroment` 文件，加入或修改：

```sh
auto_proxy="https://pac.eaimty.com/autoproxy.pac"
AUTO_PROXY="https://pac.eaimty.com/autoproxy.pac"
```

就可以设置 system-wide proxy。
