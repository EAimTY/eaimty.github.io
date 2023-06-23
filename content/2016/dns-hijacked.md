+++
title = "DNS 被劫持，已修复"
date = 2016-04-29

[extra.comments]
issue_id = 13

[[extra.comments.addition]]
user = { login = "ortt", avatar_url = "https://secure.gravatar.com/avatar/867ae57702dd9d4ebd40b2977e63497e?d=retro" }
created_at = "2023-02-23T13:27:16Z"
body = "> 然后你发现某一天你网站被墙了23333\n\n刚测了一下，  现在还正常能访问。作者有点东西"

[[extra.comments.addition]]
user = { login = "Benny", html_url = "https://www.bennythink.com/", avatar_url = "https://secure.gravatar.com/avatar/38a94d1b56f913b2531ab3a36577d3e4?d=retro" }
created_at = "2016-05-07T03:51:50Z"
body = "加个友链呀！顺手给你的“关于”里的邮箱发了封邮件！"

[[extra.comments.addition]]
user = { login = "Misaka", html_url = "https://www.twznow.com", avatar_url = "https://secure.gravatar.com/avatar/0b7a4c2253965e346881bf944d17781f?d=retro" }
created_at = "2016-05-11T12:50:16Z"
body = "然后你发现某一天你网站被墙了23333"

[[extra.comments.addition]]
user = { login = "EAimTY", html_url = "https://www.eaimty.com/", avatar_url = "https://secure.gravatar.com/avatar/f64037e002ad7b01fe9ee60351bfd1d2?d=retro" }
created_at = "2016-05-07T05:13:26Z"
body = "> 加个友链呀！顺手给你的“关于”里的邮箱发了封邮件！\n\n好哒`_(:з」∠)_`已加"
+++

~~心中有一万只草泥马在奔腾ing...~~

晚上升级了一下 Chrome，顺手把浏览数据清除了。之后进了一下博客，输入网址时没有写 HTTPS，结果发现出问题了：

不通过 HTTPS 访问博客时会被 DNS 劫持！( ´Д｀)=3

**从今天起，eaimty.xyz 及其所有子域名全部启用 SSL 加密，且全部开启 HSTS Preload**
顺便在服务器上 yum update 了一下，发现 Nginx 官方 yum 源里的软件包升级到了 1.10，终于原生支持 http2 了，~~不容易啊~~
