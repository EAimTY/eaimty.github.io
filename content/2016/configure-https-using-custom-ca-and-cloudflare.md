+++
title = "用自签名 SSL 证书配合 CloudFlare 免费 SSL 构建全站 HTTPS"
date = 2016-10-23

[extra.comments]
issue_id = 17

[[extra.comments.addition]]
user = { login = "EAimTY", html_url = "https://www.eaimty.com/", avatar_url = "https://secure.gravatar.com/avatar/f64037e002ad7b01fe9ee60351bfd1d2?d=retro" }
created_at = "2017-08-27T20:43:18Z"
body = "> 您好!博主，看了你的文章，决定按照您的方法一步一步执行，但是到了这一步不太理解了，麻烦指导一下，万分感谢！\n> 然后在你签发证书的工作目录中：\n> \n> `# mkdir -p ca/newcerts`\n> \n> `# touch ca/index.txt`\n> \n> `# touch ca/serial`\n> \n> `# echo \"01\" > ca/serial`\n> \n> 这里的目录指的是certificate 这个目录么？还是直接在root 目录执行以上代码？\n\n抱歉文章中有一处没有说清楚...建立certificate目录后"

[[extra.comments.addition]]
user = { login = "Michael翔", html_url = "https://michaelxoxo.github.io/", avatar_url = "https://secure.gravatar.com/avatar/ac8f4a8e0833adc4f1aa7ff6e3802c53?d=retro" }
created_at = "2023-03-28T15:35:23Z"
body = "博主，我是通过 acme 安装的免费证书在 NAS 上的，通过在 NAS 上的反向代理设置，https://我的域名:端口 A，是可以正常访问服务了，但是我 NAS 上其他的服务，我现在通过http://域名:端口 B访问，它会自动给我改写为 https://域名:端口 B，这是为什么呢？希望能解惑"

[[extra.comments.addition]]
user = { login = "wang", html_url = "https://www.wchb7.com/", avatar_url = "https://secure.gravatar.com/avatar/e40dec05817892eb6361ea9939bc5508?d=retro" }
created_at = "2018-05-19T14:38:02Z"
body = "eaimty.com  怎么实现的 自动跳转 到 www.eaimty.com?"

[[extra.comments.addition]]
user = { login = "EAimTY", html_url = "https://www.eaimty.com/", avatar_url = "https://secure.gravatar.com/avatar/f64037e002ad7b01fe9ee60351bfd1d2?d=retro" }
created_at = "2018-05-21T14:29:37Z"
body = "> eaimty.com  怎么实现的 自动跳转 到 www.eaimty.com?\n\n我用的是Cloudflare里的Page Rules，按照hsts标准先把`http://*eaimty.com/*`301到`https://$1eaimty.com/$2`，然后把`https://eaimty.com/*`301到`https://www.eaimty.com/$1`\n用Nginx也可以很方便地做跳转，网上有很多教程的"

[[extra.comments.addition]]
user = { login = "xuefeng", avatar_url = "https://secure.gravatar.com/avatar/fbad5fab2c900453de6b4d233b41d5a6?d=retro" }
created_at = "2017-08-30T03:08:16Z"
body = "> > 您好!博主，看了你的文章，决定按照您的方法一步一步执行，但是到了这一步不太理解了，麻烦指导一下，万分感谢！\n> > 然后在你签发证书的工作目录中：\n> > \n> > `# mkdir -p ca/newcerts`\n> > \n> > `# touch ca/index.txt`\n> > \n> > `# touch ca/serial`\n> > \n> > `# echo \"01\" > ca/serial`\n> > \n> > 这里的目录指的是certificate 这个目录么？还是直接在root 目录执行以上代码？\n> \n> 抱歉文章中有一处没有说清楚...建立certificate目录后\n\n好的，感谢您的回复，我试试，就是说mkdir certificate 后 要cd 到 certificate 路径后再执行例如下面的语句对吧？\n\n然后签发一个根域名的CA证书，第一步创建一个私钥ca.key：\n\n`# openssl genrsa -des3 -out ca.key 2048`"

[[extra.comments.addition]]
user = { login = "Simona", avatar_url = "https://secure.gravatar.com/avatar/b7eefb5e179445889ddb58d908a7d584?d=retro" }
created_at = "2023-05-16T07:53:39Z"
body = "请教下检查 nginx ， 出现 nginx: [emerg] cannot load certificate \"/root/certificate/domain.com.csr\": PEM_read_bio_X509_AUX() failed (SSL: error:0480006C:PEM routines::no start line:Expecting: TRUSTED CERTIFICATE)  是怎么回事呢？"

[[extra.comments.addition]]
user = { login = "gavincook", avatar_url = "https://secure.gravatar.com/avatar/4f41d8f36dbc40ab75af3d8527e48e00?d=retro" }
created_at = "2023-05-16T02:30:16Z"
body = "可以直接将CloudFlare的SSL/TLS模式调整为：Flexible，然后自己的服务可以基于以http的方式提供，用户以https的方式访问"

[[extra.comments.addition]]
user = { login = "xuefeng", avatar_url = "https://secure.gravatar.com/avatar/fbad5fab2c900453de6b4d233b41d5a6?d=retro" }
created_at = "2017-08-27T14:39:50Z"
body = "您好!博主，看了你的文章，决定按照您的方法一步一步执行，但是到了这一步不太理解了，麻烦指导一下，万分感谢！\n然后在你签发证书的工作目录中：\n\n`# mkdir -p ca/newcerts`\n\n`# touch ca/index.txt`\n\n`# touch ca/serial`\n\n`# echo \"01\" > ca/serial`\n\n这里的目录指的是certificate 这个目录么？还是直接在root 目录执行以上代码？"
+++

之前一段时间，本博客一直在用 letsencrypt 的 SSL 证书。这个证书的优点是免费，然而缺点也很明显，就是使用起来比较麻烦，而且有效期只有 3 个月。
我是个懒人，所以能一劳永逸的事，我都会一次性做完。很早之前就了解到 CloudFlare 有免费的 SSL 证书，但是假如仅在 CloudFlare 后台中开启它的话，并不能做到全站加密，只能开启 Flexible 模式，而不是 Full 模式。

进入 CloudFlare 后台->Crypto，可以看到第一项就是免费提供的 SSL。
这里有4种模式可选：Off（关闭 SSL）、Flexible（视情况而定）、Full（全部加密，需要在服务器上部署证书，但 CloudFlare 不会检查证书的有效性）、Full(Strict)（严格模式，也就是全部加密，而且 CloudFlare 会检查服务器上的证书是否有效）。
在这里，我们的目标是开启 Full 模式，实现方式是：首先自签一个泛域名的证书，然后在 Nginx 中设置通过 HTTPS 访问网站，最后到 CloudFlare 中设置 SSL 模式。
当然，如果你想要开启 Full(Strict) 模式，也是可以的，但服务器上就不能部署自签名的证书了，而需要一个有效机构颁发的证书，例如 letsencrypt 的证书，然而怎么做的缺点就是需要定期续期。

使用自签名证书就不存在这个问题了，你想签多长时间的有效期就可以签多长时间~~（我签了 20 年，2333333）~~，更换服务器时，也仅需要把证书文件拷到新服务器上。

<!--more-->

# 准备工作

第一步，当然是确保你的 DNS 是 CloudFlare 的！

第二步，安装 OpenSSL

debian 系：

`# apt-get install openssl`

rhel 系：

`# yum install openssl`

# 签发自签名证书

首先建立一个生成、存放证书的目录：

`# mkdir certificate`

进入该目录，然后签发一个根域名的 CA 证书，第一步创建一个私钥 `ca.key`：

`# openssl genrsa -des3 -out ca.key 2048`

第二步，生成 CA 根证书（公钥）：

`# openssl req -new -x509 -days 7305 -key ca.key -out ca.crt`

命令中，`-days` 后面的 `7305` 是指证书的有效期，以天为单位，这里设置成了 20 年，手动滑稽。

执行命令后会让你填一堆地区、组织什么的东西，随便填就好，但注意期间会让你填写 `common name`，也就是域名，这里填入的是你的根域名，例如 `eaimty.com`。最后，你就得到了一个根域的 CA 证书。

之后生成一个给泛域名用的私钥：

`# openssl genrsa -des3 -out yourdomain.com.pem 1024`

解密私钥：

`# openssl rsa -in yourdomain.com.pem -out yourdomain.com.key`

生成签名请求：

`# openssl req -new -key yourdomain.com.pem -out yourdomain.com.csr`

这一步中 `common name` 要填入泛域名，如 `*.eaimty.com`，这样生成的证书可以供所有子域使用。

下一步还不能直接执行签名，否则会报错，要先修改一下 openssl 的配置文件：

`# vi /etc/pki/tls/openssl.cnf`

找到其中的 `dir = `，把值改成 `./ca`。

然后在你签发证书的工作目录中：

`# mkdir -p ca/newcerts`

`# touch ca/index.txt`

`# touch ca/serial`

`# echo "01" > ca/serial`

这样就可以正常执行签名了：

`# openssl ca -policy policy_anything -days 7305 -cert ca.crt -keyfile ca.key -in yourdomain.com.csr -out yourdomain.com.crt`

这一步中的参数和上一步中的意义相同。
最后你会得到一个 `yourdomain.com.crt` 文件，把 `ca.crt` 中的内容粘贴到 `yourdomain.com.crt` 的最后，证书就签发完成了。

准备好 `yourdomain.com.crt`（网站证书）和 `yourdomain.com.key`（网站私钥），开始配置 Nginx！

# 配置 Nginx

这一步很简单，找到你的网站（所签发泛域名的所有子域名都可以用）的 Nginx 配置文件（通常是 `/etc/nginx/conf.d/` 下的 XXX.conf）

修改 `server{}` 段 `listen 443 ssl`，并在其下添加：

```conf
ssl_certificate /path/to/yourdomain.com.crt;
ssl_certificate_key /path/to/yourdomain.com.key;
```

测试 Nginx 的配置文件是否有错：

`# nginx -t`

注意看是否报错。

没有问题的话，重启 Nginx。

# 设置 CloudFlare

## 开启 CloudFlare SSL

进入 CloudFlare 管理界面，将 Crypto->SSL 改为“Full”。

现在通过浏览器进入 `https://你的网站/`，你就会发现小绿锁出现了！就说明我们成功了！

## 设置 HTTP 请求的重定向

虽然小绿锁出现了，但是假如访客直接输入没有声明协议的网址访问网站，默认走的仍是没有 SSL 保护的 HTTP。

我们可以通过 Nginx 来设置，也可以通过 CloudFlare 来设置。我的所有子域名都开启了 SSL，所以我是用 CloudFlare 设置的，更方便。

进入 CloudFlare 后台中的 Page Rules 选项卡，新建如下规则：

![](/pictures/vRlUMux1IT62qYr.png)

这样就可以一次性为所有子域设置重定向了。

然后记得关闭服务器上的 80 端口，以使强制通过 HTTP 协议时无法访问网站。

想要更加安全的话，CloudFlare 的后台中也有 HSTS 的设置，配置好后去 <https://hstspreload.org> 提交你的域名，就完成全站 HSTS 啦！
