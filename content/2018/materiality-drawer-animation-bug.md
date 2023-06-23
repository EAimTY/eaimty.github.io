+++
title = "materiality 主题侧边栏开启关闭动效无法显示的原因（Cloudflare）"
date = 2018-01-07

[extra.comments]
issue_id = 24

[[extra.comments.addition]]
user = { login = "Tony", html_url = "https://taotao.io", avatar_url = "https://secure.gravatar.com/avatar/165b4e01e20d7a5812e565695d1db79e?d=retro" }
created_at = "2018-02-28T05:27:36Z"
body = "以前还真没注意呢，我也关了吧"
+++

很久之前就发现这个问题了，一直无法解决...现在终于找到问题所在了。

![](/pictures/5a5228a0ea252.png)

Cloudflare 在 Speed 选项卡中有一个 Rocket Loader™ 功能，会导致一大堆问题。
这个功能貌似还是 Beta 阶段，如果手贱开启了，并且网站语言是中文（主要浏览者在中国大陆），那就赶快关闭吧。

目前来看，这个功能起不到任何加速作用，甚至还会拖慢加载速度，开启这个功能后每次浏览网站时都会从 Cloudflare 的服务器上加载一大堆没卵用的 JS，现阶段的兲朝网络下想连接 Cloudflare 的服务器？呵呵...
