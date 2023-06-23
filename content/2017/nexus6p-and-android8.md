+++
title = "Nexus 6P 开箱与 Android 8.0 奥利奥体验"
date = 2017-08-29

[extra.comments]
issue_id = 23

[[extra.comments.addition]]
user = { login = "布偶君", html_url = "https://shadowrz.wordpress.com", avatar_url = "https://secure.gravatar.com/avatar/c7a2d26d2148bed9f5d03bb6cb58b612?d=retro" }
created_at = "2017-09-24T00:31:09Z"
body = "> 不过在国内很鸡肋，毕竟毒瘤喂屎你也必须吃。\n\n为何？"
+++

纽约时间 2017 年 8 月 21 日下午 2 时 40 分左右，东美地区的日食达到了食甚阶段，纽约一片漆黑...就在这时，Google 发布了今年的最新 Android 系统 Android8.0 Oreo（奥利奥）。

很巧的是，我刚从闲鱼淘到一部 Nexus6P，正好可以抢先品尝最新的甜点！

<!--more-->

# Nexus6P 开箱

这台机器是 n6p 港版，32GB 存储，银色，卖家描述是 95 新，个人感觉大概 9 成新左右吧，裸机。充电器 5V3A，不确定是不是 QC 协议，速度不错。

先来一张正脸：

![](/pictures/59a4f937f07fe.jpg)

照片是用朋友的菊花为麦芒 4 拍摄的，加上时间是晚上，所以不太清晰。

前置双扬声器组成的对称设计很漂亮。

后盖：

![](/pictures/59a4f9f78321d.jpg)

边角处有些磕碰:

![](/pictures/59a4fa4fa2875.jpg)

# Android 8.0 体验

拿到机器后直接解掉 bootloader 锁刷了 Google 原厂镜像。

开机速度提升是这次更新的一大噱头，体验了一下，去除显示“Device Unlocked”画面的时间，的确非常快！

首次开机走完设置向导进入桌面后被吓到了：

![](/pictures/59a4fb3504c70.png)

卧槽怎么是圆角矩形？？？

Google 你疯了吗？？

打开应用抽屉（无视掉我安装的那些应用）：

![](/pictures/59a4fb94d4faf.png)

连 Google 自家的应用图标都是有的方有的圆。

设置的配色改了。一片白，快瞎了...

![](/pictures/59a50008f0af8.png)

选项层级大改一番，也砍掉了 drawer，个人不太喜欢，显得很繁琐，找个选项都变得很难。

设备信息：

![](/pictures/59a503136308a.png)

## 新功能

### Picture-in-picture（画中画）

设置中可以找到 pip 的选项（这个选项我也找了半天...）：

![](/pictures/59a5052ad1fd2.png)

其中想开启 YouTube 的 pip 需要订阅 YouTube Red，穷...用 Google Map 做个示范。打开导航，直接按 home 回到桌面，就能激活 pip：

![](/pictures/59a507ee75147.png)

可以随意拖动，拖动到屏幕最底部可以关闭。

### 安全方面

加入了 Play Protect，能定期检查设备中的 app 有没有毒。不过在国内很鸡肋，毕竟毒瘤喂屎你也必须吃。

![](/pictures/59a508aed2397.png)

另外，之前版本中的“允许安装来源未知的应用”也有变化，在 O 中可以对每个应用单独设置是否允许该应用安装新应用。

### 更全面的权限管理

![](/pictures/59a50905e2be1.png)

可以设置更多的权限，比起 6.0 和 7.x 也是一种进步吧。

### 通知系统改进

新系统引入了 Notification channels（通知通道）。

![](/pictures/59a5098da84bb.png)

如图，另外新加入了 notification dot，可以翻译成“通知点” 233，不过貌似在 Google Now Launcher 上这功能失效了...

### 新 emoji

丑出翔

![](/pictures/59a50b906869e.png)

扁平的感觉被 Google 吃了

### 新后台限制机制

后台运行的应用会强制显示在状态栏上。和7.x不同的是，这个通知暂时无法消去，强迫症福利。

![](/pictures/59a50d38b7365.png)

### 新的状态栏设计

这部分不吐槽了，因为实在是无力吐槽了。一片惨白，快瞎了。

不过好消息是，Android 6.0 上的快捷开关风格回来了，例如，现在直接点击 WiFi 按钮会 toggle 状态，点击下面的下箭头才会打开 WiFi 选择菜单。

### 彩蛋

![](/pictures/59a50ea8e074d.png)

长按后会出现：

![](/pictures/59a50ed17f0ec.png)

阴吹思婷

![](/pictures/59a50f10043b7.png)

拖动这只章鱼，它会变成~~奇奇怪怪的样子~~

# 总结

~~在总结前，我先去刷个 rr 或 purenexus 回到 7.1 吧~~

如果你手持 Nexus 5X 或 6P 或 Pixel 系列，升级尝个鲜还是不错的，但暂时不建议作为主力系统使用。

首先，这一代的显性升级比较少，而且有些新设计说实话并不是很棒...例如新的配色、后台运行应用的通知、新的 emoji 之类的。

第二，历来升级系统的经验就是：在新系统发布后，先等 1 个月左右再作为主力使用。因为发布 1 个月后，各种 app 的配置基本已经做的差不多了。另外现在 8.0 的 bug 还有很多，我这里就遇到了一个：在 play 里下载应用的时候只能一次点击下载一个应用，想要下载下一个需要等上一个应用彻底安装完成，否则第二个应用不会自动下载，很麻烦。

等到 AOSP 源码放出，Lineage 15 发布后再升级也不迟，毕竟 Lineage 才是推广新系统的主力军，就算 Google stock 也有些功能是缺失的。
