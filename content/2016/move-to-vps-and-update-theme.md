+++
title = "博客搬到了 VPS 上，顺便更新一下主题"
date = 2016-04-23

[extra.comments]
issue_id = 12

[[extra.comments.addition]]
user = { login = "TAO", html_url = "https://tao.im", avatar_url = "https://secure.gravatar.com/avatar/0d02416331f0836194acb50500826d4e?d=retro" }
created_at = "2016-06-01T02:22:54Z"
body = "备案信息亮了。。😂\n话说我也用 CloudFlare + 搬瓦工"

[[extra.comments.addition]]
user = { login = "面码酱", html_url = "http://9i3.cn", avatar_url = "https://secure.gravatar.com/avatar/8c4b18b88ffa6d3cb8e813a491f6498f?d=retro" }
created_at = "2016-04-23T15:55:50Z"
body = "vps买不起/(ㄒoㄒ)/~~，备案也是好麻烦的说，我还是我弟弟帮我备案的w(ﾟДﾟ)w"

[[extra.comments.addition]]
user = { login = "EAimTY", html_url = "https://www.eaimty.com/", avatar_url = "https://secure.gravatar.com/avatar/f64037e002ad7b01fe9ee60351bfd1d2?d=retro" }
created_at = "2016-04-24T03:39:49Z"
body = "> vps买不起/(ㄒoㄒ)/~~，备案也是好麻烦的说，我还是我弟弟帮我备案的w(ﾟДﾟ)w\n\n现在有很多便宜VPS的，我这台一年才200人民币。至于备案...反正我的域名、VPS、DNS都不是国内的🤣"

[[extra.comments.addition]]
user = { login = "EAimTY", html_url = "https://www.eaimty.com/", avatar_url = "https://secure.gravatar.com/avatar/f64037e002ad7b01fe9ee60351bfd1d2?d=retro" }
created_at = "2016-06-01T15:42:07Z"
body = "> 备案信息亮了。。😂\n> 话说我也用 CloudFlare + 搬瓦工\n\n感觉自从10刀每年的套餐买完之后，搬瓦工的性价比已经不高了...准备今年暑假试试DO或者Vultr😂"
+++

总结一下对服务器安全的简单设置。

<!--more-->

最近，~~墙又高了很多，中国电信开始大量墙境外主机和 VPS 的 IP，~~OpenShift 在国内访问很不稳定，经常出现 timeout，再加上 OpenShift 用的是 Apache，本身就不擅长处理动态程序，导致博客频频 503...实在是不能忍了...突然想起前些天手贱多买了一台 VPS，于是折腾了一下，把博客搬到了搬瓦工的洛杉矶机房。
这回从国内访问的速度快多了，不过貌似 CloudFlare 的 DNS 已经快要被墙了...🤣

然而在向新服务器导入 MySQL 数据库的时候忘记先删除安装时自动新建的数据库了，也就是把 utf8mb4 编码的数据库导入成了 uft8...
结果就是排版和 emoji 都没了...没办法，重新排吧┐(‘д’)┌

转移过程中顺便改了改主题，添加了一些功能，改了一下配色，顺便更新了一下 bootstrap 和 jquery（没办法，强迫症┐(‘д’)┌），压缩了一下 Material-ScrollTop 的 css 和 js，还把几乎所有代码的格式统一了，~~显得很规整~~（没办法，强迫症┐(‘д’)┌）

更新后的主题依然在 GayHub 上：

<https://github.com/EAimTY/typecho_material_theme>
