+++
title = "新的 materiality-typecho-theme"
date = 2020-03-24

[extra.comments]
issue_id = 37

[[extra.comments.addition]]
user = { login = "v投笔从容v", html_url = "https://shanpeng.wang", avatar_url = "https://secure.gravatar.com/avatar/beef0d08b40887f978689a6f76129ea2?d=retro" }
created_at = "2020-05-16T08:46:57Z"
body = "> > 是屏蔽了代码高亮吗?怎么恢复\n> \n> 代码高亮并非是被屏蔽了，而是MDUI并没有这个功能\n> 主题的下个版本应该会加入代码高亮功能😀\n\n同求代码高亮功能，找了一些typecho插件，都不好用。"

[[extra.comments.addition]]
user = { login = "v投笔从容v", html_url = "https://shanpeng.wang", avatar_url = "https://secure.gravatar.com/avatar/beef0d08b40887f978689a6f76129ea2?d=retro" }
created_at = "2020-05-16T08:47:42Z"
body = "> > 是屏蔽了代码高亮吗?怎么恢复\n> \n> 代码高亮并非是被屏蔽了，而是MDUI并没有这个功能\n> 主题的下个版本应该会加入代码高亮功能😀\n\n我还想问一下，你用的什么插件支持LaTeX公式啊？"

[[extra.comments.addition]]
user = { login = "萤石", html_url = "https://fluors.net", avatar_url = "https://secure.gravatar.com/avatar/3dc4b15056f053375886581c6e1f2221?d=retro" }
created_at = "2020-05-12T09:28:39Z"
body = "是屏蔽了代码高亮吗?怎么恢复"

[[extra.comments.addition]]
user = { login = "学神之女", html_url = "https://www.dffzmxj.com", avatar_url = "https://secure.gravatar.com/avatar/118fb7985313497795358632967595b5?d=retro" }
created_at = "2020-07-06T13:22:17Z"
body = "你的主题应该会很稳，在MDUI首页挂着的网页中，有不少是挂掉的。\n\n至于Material Design 2……最不满意的就是很圆润的圆角。\n\n另外，有些动效实现起来好累人。\n\n右下角备案信息很出戏😂。"

[[extra.comments.addition]]
user = { login = "油油", html_url = "https://www.200011.net", avatar_url = "https://secure.gravatar.com/avatar/d9735fa38f5f2dd01865ac6b873585e9?d=retro" }
created_at = "2020-04-09T12:12:28Z"
body = "太棒了吧，我的免费放出来都没人要"

[[extra.comments.addition]]
user = { login = "EAimTY", html_url = "https://www.eaimty.com/", avatar_url = "https://secure.gravatar.com/avatar/f64037e002ad7b01fe9ee60351bfd1d2?d=retro" }
created_at = "2020-11-02T03:10:50Z"
body = "> > > 是屏蔽了代码高亮吗?怎么恢复\n> > \n> > 代码高亮并非是被屏蔽了，而是MDUI并没有这个功能\n> > 主题的下个版本应该会加入代码高亮功能😀\n> \n> 同求代码高亮功能，找了一些typecho插件，都不好用。\n\n新版本已经支持代码高亮了😃"

[[extra.comments.addition]]
user = { login = "初夏阳光", avatar_url = "https://secure.gravatar.com/avatar/fc32f14ea2531cfd9415c9e0b3faa905?d=retro" }
created_at = "2020-04-25T10:14:32Z"
body = "> 太棒了吧，我的免费放出来都没人要\n\n主题嘛，自己写写抱着玩一玩的态度就好了。\n\n要我说主题写的太完美的话，用同一个主题的人太多，感觉会审美疲劳 233"

[[extra.comments.addition]]
user = { login = "EAimTY", html_url = "https://www.eaimty.com/", avatar_url = "https://secure.gravatar.com/avatar/f64037e002ad7b01fe9ee60351bfd1d2?d=retro" }
created_at = "2020-05-12T16:38:37Z"
body = "> 是屏蔽了代码高亮吗?怎么恢复\n\n代码高亮并非是被屏蔽了，而是MDUI并没有这个功能\n主题的下个版本应该会加入代码高亮功能😀"
+++

时间过的真快啊，从 2017 年 3 月 16 日我提交了这个主题的首个 commit，到现在已经整整 3 年过去了。
在之前的三年里作为一个完成度极低的主题能收获几十个 star 我已经很满意了，不过经过这两个月间的重构与完善，这个主题应该会更好用，所以特意写了这篇文章来宣传😂

当初这个博客刚刚建立时，由于我对 MD 的无脑粉，我使用了 [HanSon/typecho_material_theme](https://github.com/Hanson/typecho_material_theme)。但是...这个主题用的是 material bootstrap，所以我总觉得缺那么一股味道...

于是，我顺藤摸瓜地找到了 Google 官方的 [Material Design Lite](https://github.com/google/material-design-lite)。我照着 HanSon 的 typecho_material_theme，用 Material Design Lite 仿造出了一个简陋的主题：[EAimTY/typecho_material_theme](https://github.com/EAimTY/typecho_material_theme)。我对前端的知识量几乎为零，所以那个主题几乎丑到不能看...

一次，我偶然发现了 [MDUI](https://www.mdui.org/) 这个库。相比与 Material Design Lite，MDUI 的完成度高得多，功能与组件丰富，用起来简单方便，而且还包含了一个 JS 工具库 `mdui.JQ`，体积比 jquery 小很多，但功能也够用。
我用 MDUI 重写了博客的主题，产物就是 [materiality-typecho-theme](https://www.eaimty.com/theme.html)。
由于我对很多前端知识只是一知半解，况且当时在上高中没有太多空余时间，一直到 2019 年末，这个主题的完成度仍旧不高。尽管如此，MDUI 的作者仍然将这个主题放在了 [MDUI 项目的首页](https://www.mdui.org/)进行展示，十分感谢！

转眼间三年就过去了，Google 推出了“更加扁平的 Material Design 2”来变相搞死 MD，我对其执着也逐渐变淡了。期间我对这个主题的更新“很不上心”。直到 2020 年寒假，我有了大把的空闲时间，才开始对这个主题的大规模修改。

为了填当年挖的坑，同时也为了让主题值得被继续“挂”在 MDUI 的官网上，我明确了这个主题要实现的功能与侧重点：简洁、文字显示，并在 2020 年初的两个月里提交了比前面三年间提交总数还多的 commits...

这是主题最近的 CHANGELOG：[CHANGELOG.md](https://github.com/EAimTY/materiality-typecho-theme/blob/master/CHANGELOG.md)

现在这个主题的完成度已经不是那么低了，所以为了保持简洁性，主题今后的更新将以修复漏洞为主，不再会像之前两个月一样非常频繁地加入新功能。
