+++
title = "将 git 仓库作为数据库的动态 CMS：hummingbird"
date = 2021-12-20

[extra.comments]
issue_id = 49

[[extra.comments.addition]]
user = { login = "凡尘", avatar_url = "https://secure.gravatar.com/avatar/60704096935c4f6f73bd6c7c862c4c2b?d=retro" }
created_at = "2022-01-20T07:34:27Z"
body = "顶一下作者"

[[extra.comments.addition]]
user = { login = "ortt", avatar_url = "https://secure.gravatar.com/avatar/867ae57702dd9d4ebd40b2977e63497e?d=retro" }
created_at = "2023-02-23T12:45:02Z"
body = "我也顶一下作者"
+++

我从今年六月开始学 Rust，到现在差不多有半年了。写 [hummingbird](https://github.com/EAimTY/hummingbird) 这个项目的想法我在八月就已经有了：写一个用 git 仓库作为数据库的内容管理系统，给 git repo 中 markdown 格式的文章套 HTML 模板，然后 serve。
这样能结合传统 CMS 和 GitHub Pages 的优点——能用对于 CMS 本身只读的 git 仓库保证数据的安全性，也能像传统的 CMS 一样提供动态内容，比如搜索文章内容，甚至支持在一次搜索中使用多个 filter 来缩小范围，还不必像（免费版） GitHub Pages 一样只能建在公开仓库上。GitHub 的仓库文件编辑器还可以直接视为管理后台。
当然这种实现也会导致一些限制，例如不能原生支持评论，能实现的功能比较少，数据库更新不能太频繁，比起 GitHub Pages 需要一台服务器来跑服务端...
hummingbird 比 WordPress 大概会快十倍吧，大概（

总之我觉得这个项目应该有适合的使用场景，所以就开始动手了。经过断断续续 4 个月的开发，终于把雏形写出来了。

写 hummingbird 的过程基本上就是我入门 Rust 的过程。每过一段时间，回头看之前写的代码就会觉得写得稀烂，想重构。这个项目的数据库实现我重写过不下五次，然而现在还是觉得写得很差。这也算是学习的过程吧。

不论从代码上看，还是从软件工程角度看 hummingbird，都有些问题——某些功能实现地很幼稚，抽象也到处漏，但我总算是写完了一个比较复杂的项目，比起之前总是纸上谈兵还是有进步的。

说说大体的实现思路吧：

整个项目主要分成 3 个部分：配置、数据库和服务器 / 路由。

配置部分很简单，就是读一个 TOML 格式的配置文件然后解析数据，没什么好说的。

数据库部分大概又能分 3 个部分吧：内存里的数据库存储部分，集成的 git 客户端，还有模板系统。
存储的实现比较原始，就是在解析过 git 仓库之后把其中的所有内容存进二叉堆排序，然后转成 Vec。另外还有些关于内容作者之类的映射。
git 部分是用 [libgit2](https://libgit2.org/) 实现的，用了 [git2-rs](https://github.com/rust-lang/git2-rs) 这个 Rust 的 bindings。这里算是写项目前期坑最多的地方，libgit2 暴露的 API 层级比较低，不像平时直接用 git 命令一样方便，而且当时还不太熟悉 Rust，实现反向遍历 commits 拿到文章作者、创建时间和更改时间花了很大力气。另外，git2-rs 的仓库抽象是 `!Send` 和 `!Sync` 的，所以刚开始写数据库时我只能做一个 `RepoGuard` 包住 git 仓库把它留在主线程用 Channel 通信，把剩下的部分 spawn 成 tasks 出去，从 git 仓库取数据的过程又臭又长。后面我发现 libgit2 的文档中只提到不能 parallel 地使用仓库，所以才会 `!Send` 和 `!Sync`。hummingbird 只有在被手动触发数据更新的时候才会操作 git 仓库，而且操作会上排他锁，所以直接把仓库标成 `Sync` 作为数据库成员走状态共享肯定没问题。
模板系统中的 markdown 解析用了 [pulldown-cmark](https://github.com/raphlinus/pulldown-cmark)，HTML 模板应用是自己手写的，因为 [tera](https://github.com/Keats/tera) 这类的模板实现实在是太重了。说实话我不是很满意现在的实现，有一大堆 clone。之后我想写一个完全 Evaluate-on-Write 的、带 Cacher 的 StringBuilder。

服务器 / 路由部分我也改过很多次。最早是用 [axum](https://github.com/tokio-rs/axum) 写的，但后来发现 axum 的很多功能我完全用不到，比如 middleware 之类的，而且 axum 有大约四百个依赖，太重了。所以我换到了 [hyper](https://github.com/hyperium/hyper)，不过就要自己解决路由的问题了。开始时我想用一张大的字典当路由表，在更新数据库的时候就把所有所有数据解析好，但是发现存储效率不是很高，改成存过程也比较难实现。最后我的解决方案是用一张字典存文章、页面和的其它的静态路径，其它有参数的路径用字典树匹配和捕获。我写了一个泛型的字典树实现，但是 bench 后发现效率比 [matchit](https://github.com/ibraheemdev/matchit) 低至少一倍，~~我还是太年轻~~，所以就用了 matchit。

hummingbird 有什么适用的具体使用场景吗？我觉得可能用来 serve 长篇的文档、说明比较好（类似于 [LLVM IR](https://llvm.org/docs/LangRef.html) 这种），用来做一个简单的博客也不错，只是需要外挂评论系统。
