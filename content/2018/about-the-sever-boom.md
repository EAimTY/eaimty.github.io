+++
title = "更换服务器，以及关于服务器炸掉的这半个月"
date = 2018-10-28

[extra.comments]
issue_id = 30

[[extra.comments.addition]]
user = { login = "丛林意志", html_url = "https://demo.clyzhi.com", avatar_url = "https://secure.gravatar.com/avatar/fc31f7134a0fe738b00b6c8306a0ccf5?d=retro" }
created_at = "2019-11-02T15:33:41Z"
body = "> 阅读mdui时，活捉到同一个框架的博主 ：）\n\n俺也一样！"

[[extra.comments.addition]]
user = { login = "一场清梦", html_url = "https://stillman.cn", avatar_url = "https://secure.gravatar.com/avatar/374d0c8dfcea6a99772c39b95eba3bdc?d=retro" }
created_at = "2018-12-15T05:27:24Z"
body = "那为什么不搬到香港，岂不是更快#滑稽"

[[extra.comments.addition]]
user = { login = "ic翼", html_url = "https://bingyishow.top", avatar_url = "https://secure.gravatar.com/avatar/0f2ae872a709008af31c192657c71f1a?d=retro" }
created_at = "2018-12-08T07:55:14Z"
body = "阅读mdui时，活捉到同一个框架的博主 ：）"
+++

我已经半年多没管网站所在的服务器了。大约在 9 月 30 号左右的某天晚上，我突然想更新一下服务器上面的软件。然而在已经 yum update 完成然后 reboot 之后，发现 ssh 不通了...这才想起貌似我用来更新内核的 elrepo-kernel 源是打开的...连上 vultr 提供的 VNC 后发现出了 kernel panic...

当时慌慌张张地用 [SystemRescueCd](http://www.system-rescue-cd.org) 拷贝出了各种配置文件、资源、database 文件后准备第二天修复，然而...

因为最近一段时间学校里比较忙，事实上是在 10 月中旬才腾出时间折腾的。

~~我是不会告诉你们鸽了这么长时间是因为舰 C 2018 初秋活动，非提自然需要花比其他提督长数倍的时间捞船的...~~

这次顺便把站点迁移到了 GCP 东京，比起 vultr 快了不少。原因的确有一部分是那 300 刀赠金，不过即使一年的有效期结束，gcp 的性价比也不错。

这里吐槽一下 GCP 的金额显示，我的 Google 账号在日区，payment profile 也是日区的，然而 GCP 在显示金额时货币单位明显是日元，但金额数后写的是一个大大的“**元**”字（简体中文页面），而非“円”，导致我以为赠金是三万多元人民币...
