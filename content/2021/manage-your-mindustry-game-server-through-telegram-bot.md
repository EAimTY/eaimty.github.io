+++
title = "使用 teledustry，通过 Telegram bot 远程管理你的 Mindustry 游戏服务器"
date = 2021-09-30

[extra.comments]
issue_id = 48

[[extra.comments.addition]]
user = { login = "hazj", avatar_url = "https://secure.gravatar.com/avatar/?d=retro" }
created_at = "2021-10-06T14:51:17Z"
body = "我又路过〔手动滑稽〕"

[[extra.comments.addition]]
user = { login = "凡尘", avatar_url = "https://secure.gravatar.com/avatar/60704096935c4f6f73bd6c7c862c4c2b?d=retro" }
created_at = "2021-10-26T03:29:41Z"
body = "作者最近更新得很频繁啊😄😄😄😃😃"
+++

最近架了个 Mindustry 游戏服务器和朋友一起玩 PvP（~~然而没玩几天就弃坑跑去 MC 了~~），感觉不错，只是每次输命令和上传地图的时候都要 ssh/sftp 到服务器上有点不方便，所以就写了个 Telegram 机器人用来输命令和上传地图：

[teledustry - Manage your Mindustry server through Telegram bot](https://github.com/EAimTY/teledustry)

我不会写 Java，所以没把这个 bot 写成 mod 的形式，而是直接把游戏服务器进程创建为子进程，然后读写子进程的 stdio。
这个 bot 用起来很简单，只要用

`$ teledustry -t API_TOKEN -u YOUR_TELEGRAM_USERNAME SERVER_FILE.jar`

就可以启动 Mindustry 服务器和 bot，然后去找 bot 聊天就能执行命令和上传地图了（记得用 `/output` 命令让 bot 把输出发到当前聊天里）。
