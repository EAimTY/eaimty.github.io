+++
title = "LNMP 环境 VPS 的常用安全设置"
date = 2016-03-26

[extra.comments]
issue_id = 11

[[extra.comments.addition]]
user = { login = "起司", html_url = "https://140ke.com", avatar_url = "https://secure.gravatar.com/avatar/f2141b41f194f655ba8676c2a62b4abe?d=retro" }
created_at = "2016-04-09T12:16:40Z"
body = "<https://140ke.com/archives/439.html>\n<https://140ke.com/search/从入侵谈防御/>\n安全跟用户基本没什么关系"
+++

总结一下对服务器安全的简单设置。

<!--more-->

# 设置 iptables

配置好防火墙是很重要的。

先清空 iptables 规则并清零：

`# iptables -F`

`# iptables -X`

`# iptables -Z`

然后设置规则，首先允许本机与外部建立通讯连接：

`# iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT`

允许 icmp 协议（也就是 ping）：

`# iptables -A INPUT -p icmp -j ACCEPT`

之后用以下命令开启 ssh（默认为 22）、http（默认为 80）、https（默认为 443）之类的端口：

`# iptables -A INPUT -p tcp --dport 端口号 -j ACCEPT`

需要开启 UDP 端口的话就执行：

`# iptables -A INPUT -p udp --dport 端口号 -j ACCEPT`

最后屏蔽其它请求：

`# iptables -P INPUT DROP`

保存并重启 iptables：

`# service iptables save`

`# service iptables restart`

# 配置 Nginx 与 PHP 用户权限

首先，我们来看一下 Nginx 和 php-fpm 运行在什么用户下：

`# ps aux|grep nginx`

`# ps aux |grep php-fpm`

可以发现，通过 yum 安装的 Nginx 默认运行在用户 `nginx` 下，php-fpm 运行在用户 `apache` 下，这样不利于安全，也不便于管理。

起初，我习惯新建一个用户并把 PHP 与 Nginx 放在其下运行，但最后发现其实这并不是最安全的做法，而且还很麻烦。

研究并参考 Google 到的一些文章后，我发现让它们在 nobody 下运行更加安全：

`# vi /etc/nginx/nginx.conf`

将 `user` 后的用户改为 `nobody`

`# vi /etc/php-fpm.d/www.conf`

搜索 `user = ` 找到后将文件中 user 与 group 的值改为 `nobody`。

这时重启一下 Nginx 与 php-fpm，就会发现它们已经是运行在 nobody 用户下的了。

但是假如仅仅修改 Nginx 与 php-fpm 的运行用户，会出现网站程序(例如 WordPress、Typecho)无法上传附件或更改文件的错误，这是因为 nobody 用户默认无法访问网页程序文件。

修改网站文件所在目录的用户：

`# chown -R nobody:nobody 网站目录`

顺便控制一下读写权限：

`# find 网站目录 -type d|xargs chmod 0755`

`# find 网站目录 -type f|xargs chmod 0644`

至此权限就配置完成了，但假如这时使用 phpMyAdmin，就会报错：

```
Warning in ./libraries/session.inc.php#*** session_start():
open(/var/lib/php/session/*************, O_RDWR) failed: Permission denied (**)
```

解决方法是：

`# chown nobody:nobody /var/lib/php/session`

假如还是有问题就执行：

`# chown -R nobody:nobody /var/lib/php/session`
