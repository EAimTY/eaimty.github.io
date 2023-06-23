+++
title = "Xuanwu - 在全角字符与半角字符间添加空格的命令行工具"
date = 2021-09-15

[extra.comments]
issue_id = 47

[[extra.comments.addition]]
user = { login = "凡尘", avatar_url = "https://secure.gravatar.com/avatar/60704096935c4f6f73bd6c7c862c4c2b?d=retro" }
created_at = "2021-09-19T03:43:17Z"
body = "抢个沙发哈哈"
+++

[Pangu](https://github.com/vinta/pangu.js) 是一个有名的用来在全角字符与半角字符间添加空格的库，支持多种语言，其中 Node.js 版 [pangu.js](https://github.com/vinta/pangu.js) 可以作为命令行前端来使用。
但是由于是 Node.js，想要用 pangu 就需要装很多依赖，类 Unix 系统有包管理还好说，在 Windows 平台下装 node 只为了用 Pangu 实在是有点麻烦。

为了解决这个问题，我写了一个 Pangu 的命令行前端 [Xuanwu](https://github.com/EAimTY/xuanwu)，基于 [pangu-rs](https://github.com/airt/pangu-rs)，非常简单，只有六十行代码😂，还没有任何依赖。

需要用 Pangu 的时候，可以直接到 [Release](https://github.com/EAimTY/xuanwu/releases) 里下载编译好的可执行文件（暂时没有 macos 的，darwin 交叉编译有点麻烦），比装 node + pangu.js 方便得多。

至于为什么叫 Xuanwu 这个名字，传说玄武大帝是盘古的儿子，这个工具基于 Pangu，那自然就要叫 Xuanwu 了😂
