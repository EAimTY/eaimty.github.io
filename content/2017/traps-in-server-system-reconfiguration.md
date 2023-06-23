+++
title = "记录一下重装服务器系统中遇到的一堆坑"
date = 2017-08-08

[extra.comments]
issue_id = 22

[[extra.comments.addition]]
user = { login = "RVIER", avatar_url = "https://secure.gravatar.com/avatar/6034259ba3ddea6565f63320769f5132?d=retro" }
created_at = "2017-10-14T09:26:49Z"
body = "> 😂SELinux，背锅侠\n\n“你抄我，我抄你，而且抄的还都有问题”  哈哈哈哈哈哈哈嗝儿"

[[extra.comments.addition]]
user = { login = "Benny", html_url = "https://www.bennythink.com", avatar_url = "https://secure.gravatar.com/avatar/38a94d1b56f913b2531ab3a36577d3e4?d=retro" }
created_at = "2017-08-09T07:19:51Z"
body = "😂SELinux，背锅侠"
+++

很长一段时间以来，博主用的 [Vultr](https://www.vultr.com/?ref=7131872) 2.5 刀小机上的 MySQL 5.7 频繁抽风，经常需要手动重启服务器，至于为什么这么长时间没有管它，原因还是我懒呗~

最近闲了下来，有时间折腾一下服务器了，于是重装了一下换回 MySQL 5.5，用 CentOS 官方镜像重装了系统。
之前因为听说 SELinux 和 Systemd 之间不容易和谐共处，所以都是关掉 SELinux 的（不要学我，这很不安全）。
这次用 CentOS7 1611 x64 Minimal 官方镜像中 SELinux 是默认打开的，试了一下，发现不是想象中的那么差，所以就没有关闭。
接下来，坑就来了。

<!--more-->

# 坑一

更换 SSH 端口后，从外界无法连接

## 解决方法：

根据 sshd_config 中给出的方法，在文件中修改端口号后需要告诉 SELinux，运行 `# semanage port -a -t ssh_port_t -p tcp 端口号` 

# 坑二

semanage command not found

## 解决方法

查一下命令所在包：`# yum provides semanage`

得知命令存在于包 `policycoreutils-python`

安装包：`# yum install policycoreutils-python`

# 坑三

nginx 读不到配置文件、证书，网站目录

## 解决方法

SELinux 有个限制：cp instead mv

在折腾好文件后，执行 `# restorecon -v -R /path/to/files`

# 坑四

网站 conf、证书、文件都到位后访问显示 `files not exist`

## 解决方法

还是 SELinux 的锅

告诉 SELinux 这是网站的文件：`# chcon -Rt httpd_sys_content_t /path/to/www`
如需通过网页程序修改文件内容，则是：`# chcon -Rt httpd_sys_content_rw_t /path/to/writabledir`

# 坑五

可通过 ip 直接经过 443 端口访问网站

## 解决方法

我的站点都是有 SSL 的，所以一般都会在 iptables 中禁掉 80 端口，所以咱们只需要折腾一下 443 端口的问题
Google 到的结果中有一堆放在 CSDN、博客园之类的文章，你抄我，我抄你，而且抄的还都有问题，真 TM 是恶心透了。

最终找到了解决方案：

在 nginx.conf 中的 `include /etc/nginx/conf.d/*.conf;` 一行下面插入：

```conf
server {
    listen  443 default;
    return  444;
    ssl_certificate /path/to/crt;
    ssl_certificate_key /path/to/key;
}
```

这里的证书自己签一个就好，注意不要在其中泄露真是的网站信息。
