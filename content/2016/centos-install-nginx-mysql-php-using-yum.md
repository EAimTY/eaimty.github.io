+++
title = "CentOS 7 下使用 yum 安装 Nginx+MySQL+PHP7.x 环境"
date = 2016-03-05

[extra.comments]
issue_id = 9

[[extra.comments.addition]]
user = { login = "yangyi", avatar_url = "https://secure.gravatar.com/avatar/6d43f594683911aeebc201245202c315?d=retro" }
created_at = "2016-06-07T09:42:54Z"
body = "写的太好了，大赞！"
+++

**2018 年 2 月 2 日更新：将系统换为 CentOS 7**

这篇文章的初始版本所适用的系统是 CentOS 6，两年后，原文章中的许多方法已不再适用。
为了保证稳定性与安全性，我们使用的所有软件都应确保是最新的稳定版本。

<!--more-->

# 配置yum源

在这一步之前最好确定原本的 yum 源是官方源。

## EPEL

首先我们需要安装 EPEL 的 yum 源。Enterprise Linux 额外软件包（Extra Packages for Enterprise Linux，EPEL）是由来自 Fedora Project 的志愿者发起的社区力量，为了创建由高质量的附加软件组成的、用于补足 RHEL 和其他兼容版本的软件仓库。可以直接用 yum 安装：

`# yum install epel-release`

## remi

安装 EPEL 源的目的，其实是为了安装另一个 yum 源：remi。remi 源依赖 EPEL 源。

`# rpm -ivh https://rpms.remirepo.net/enterprise/remi-release-7.rpm`

`# rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-remi`

到这里 remi 源就安装好了，但我们需要手动启用 remi 的 PHP 专用源：

`# ls /etc/yum.repos.d/`

可以看到有 `remi-php70.repo`、`remi-php71.repo`、`remi-php72.repo` 等文件。
如果将 `remi-php70.repo` 中 `[remi-php70]` 段的 `enabled=0` 改为 `enabled=1`，之后安装的就是 PHP7.0
如果将 `remi-php71.repo` 中 `[remi-php71]` 段的 `enabled=0` 改为 `enabled=1`，之后安装的就是 PHP7.1
如果将 `remi-php72.repo` 中 `[remi-php72]` 段的 `enabled=0` 改为 `enabled=1`，之后安装的就是 PHP7.2
以此类推

这时运行：

`# yum list php`

假如看到版本号是你所希望安装的版本，就说明换 remi 源成功了。

## MySQL
注意，MySQL 版本号并不是越大越好
目前 MySQL 的大版本号有 5.5、5.6、5.7、8.0（开发中，不建议用于生产环境中）
一般来说，这四个大版本的版本号越大，相应的就需要越强的性能来驱动

MySQL 官方的换源教程非常详细，可以直接参考：
<https://dev.mysql.com/downloads/repo/yum>

## Nginx

最后要解决的是 Nginx 换源，推荐使用 Nginx 官方的源：

`# rpm -ivh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm`

至此，换源工作就彻底完成了。

# 安装软件包

这一步简单，用 yum 一股脑装上即可:

`# yum install nginx mysql-community-server php-fpm`

还需要安装一些 PHP 的组件，按需求安装，这里只安装常用的一些：

`# yum install php-pdo php-mysql php-gd php-intl php-mbstring php-bcmath`

# 配置软件

## 开启自启

先来把这堆东西的自启动打开：

`# systemctl enable nginx`

`# systemctl enable mysqld`

`# systemctl enable php-fpm`

如果机器上同时装了 Apache，记得要禁止它自启，防止它与 Nginx 抢端口：

`# systemctl disable apache`

## 配置 Nginx

可以删掉 Nginx 默认的配置文件与目录：

`# rm -rf /etc/nginx/conf.d/default.conf /usr/share/nginx`

新建一个配置文件：

`# vi /etc/nginx/conf.d/www.conf`

输入以下内容：

```conf
server {
    listen       80;
    server_name  localhost; //这里改成你的域名
    root         /home/www; //这里改成准备放网页文件的目录
    index        index.php index.html index.htm;

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```

然后修改一下 Nginx 允许上传最大文件的大小，并禁止 Nginx 在 HTTP Header 中发送服务器信息：

`# vi /etc/nginx/nginx.conf`
在 `include /etc/nginx/conf.d/*.conf;` 这一行上面插入两行：

```conf
client_max_body_size 200m;
server_tokens off;
```

这样 Nginx 就配置完了，下面配置 MySQL

## 配置 MySQL

启动 MySQL：

`# systemctl start mysql`

`# mysql_secure_installation`

这时会进入 MySQL 的安装向导中，按照步骤完成即可。

## 配置 PHP

只剩下 PHP 的配置了。
修改一下 PHP 的配置文件就可以了：

`# vi /etc/php.ini`

首先修改 PHP 允许上传最大文件的大小和数量：
搜索 `upload_max_filesize=2M` ，改为 `upload_max_filesize=200M`，这里的 200M 就是最大可上传的文件大小；
搜索 `max_file_uploads=2`，改为 `max_file_uploads=200`，这里的 200 就是一次性最多可上传的文件数。

使 HTTP Header 中不显示 PHP 信息，搜索 `expose_php = On`，改为 `expose_php = Off`。

修改时区：

在 vi/vim 命令模式中键入 `/date.timezone` 来查找 `date.timezone` 字符串
找到 `;date.timezone =` 这一行，取消掉注释（就是删掉行首的分号），然后将其值设置为 `"Asia/Shanghai"`。

关于PHP所支持的时区列表，参见 <https://www.php.net/manual/zh/timezones.php>

最后这一行长这样：

```conf
date.timezone = "Asia/Shanghai"
```

另外，如果使用的是 Typecho 程序，需要打开 PHP 的 pathinfo：
搜索 `cgi.fix_pathinfo = `，取消掉行首的注释，把参数改为 1，也就是：

```conf
cgi.fix_pathinfo = 1
```

# 测试LNMP

首先启动这些软件：

`# systemctl start nginx`

`# systemctl start mysqld`

`# systemctl start php-fpm`

建立 web 目录：

`# mkdir /home/www`（就是在配置 Nginx 那一步所设置的存放网站文件的目录）

放一个 phpinfo：

`# vi /home/www/index.php`

输入：

```php
<?php phpinfo(); ?>
```

这时访问你的域名或 IP，不出意外就可以看到 phpinfo 了。

至此，LNMP 环境就安装完成了，把你的网页放在设置好的目录下，全世界就可以访问你的站点了。
