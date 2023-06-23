+++
title = "网页自适应系统暗色模式的 W3C 新规范：prefers-color-scheme"
date = 2020-08-02

[extra.comments]
issue_id = 40

[[extra.comments.addition]]
user = { login = "wu先生", html_url = "http://wuziya.com", avatar_url = "https://secure.gravatar.com/avatar/e08b1ed883704d10a4e4b9c05b2bf019?d=retro" }
created_at = "2020-09-24T07:03:32Z"
body = "我不喜欢暗色，特别是不经我同意，直接切换的。"

[[extra.comments.addition]]
user = { login = "叶开楗", html_url = "https://叶开.cn", avatar_url = "https://secure.gravatar.com/avatar/4741124456fb02fa4da30494dbc6c99e?d=retro" }
created_at = "2021-12-16T04:01:44Z"
body = "感觉很可以！"

[[extra.comments.addition]]
user = { login = "Hans Wan", avatar_url = "https://secure.gravatar.com/avatar/6665c913fa489b415d2946e066dbf427?d=retro" }
created_at = "2020-08-17T23:26:00Z"
body = "写的很好，最后张某龙那个有被笑到😂😂。"

[[extra.comments.addition]]
user = { login = "EAimTY", html_url = "https://www.eaimty.com/", avatar_url = "https://secure.gravatar.com/avatar/f64037e002ad7b01fe9ee60351bfd1d2?d=retro" }
created_at = "2020-09-28T01:27:09Z"
body = "> 我不喜欢暗色，特别是不经我同意，直接切换的。\n\n这不是“自动”切换，系统的暗色模式状态是由用户自己控制的。"
+++

近几年，各个主流操作系统都逐渐开始重视暗色模式，从而改善用户在环境光亮低时的阅读体验。随着水果在 iOS 13 与 macOS Mojave 中添加了暗色模式，除了 Linux 以外所有的主流操作系统都已经实现了系统层级的暗色模式。Linux 由于 DE、WM 的种类繁杂暂时没有统一的标准，但目前可以通过暗色 GTK+ 主题、浏览器插件等方式实现“伪全局”暗色模式。既然有了系统层级的适配，浏览器就可以读取暗色模式开关，从而实现网页的自适应。这就是新标准 `prefers-color-scheme`。

<!--more-->

# `prefers-color-scheme` 是什么

W3C 在 2020 年 7 月 31 日发布的 [Media Queries Level 5 标准草案](https://www.w3.org/TR/mediaqueries-5/)中提到了新的属性 `prefers-color-scheme`，网页现在可以通过条件规则组来获取浏览器宿系统的暗色模式状态并应用了。也就是说，现在我们可以很简单地实现“暗色模式系统访问的页面是暗色的，亮色模式系统访问的页面是亮色的”。

# `prefers-color-scheme` 怎么用

可以参考 MDN 关于 `prefers-color-scheme` 的[描述](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-color-scheme)。

`prefers-color-scheme` 有 2 种值：
- `light`——浏览器宿系统使用亮色主题的界面，同时也是默认值，浏览器 `privacy.resistFingerprinting` 被设置为 `true` 时返回的也将是这个值
- `dark`——浏览器宿系统使用暗色主题的界面

还有一个已废弃的值：
- `no-preference`——浏览器宿系统使用未知主题的界面，当较旧版本的浏览器在宿系统不支持系统层级的暗色模式时会返回这个值，较旧版本的浏览器 `privacy.resistFingerprinting` 被设置为 `true` 时返回的也将是这个值

通过条件规则组就可以作出判断。

## CSS

    @media (prefers-color-scheme: dark) {
      // 暗色模式样式
    }
    @media not (prefers-color-scheme: dark) {
      // 非暗色模式样式
    }

## JavaScript

只使用CSS条件规则很难实现某些需求，我们可以对 `window` 使用 `matchMedia` 方法得到的 Media 使用 `matches` 方法来获取系统暗色模式状态：

    if (window.matchMedia('prefers-color-scheme: dark').matches) {
      // 是暗色模式做什么
    } else {
      // 非暗色模式做什么
    }

另外还可以监听系统暗色模式的状态，在系统开关暗色模式时作出反应：

    window.matchMedia('(prefers-color-scheme: dark)').addListener(e => {
      if (e.matches) {
        // 系统开启暗色模式后做什么
      } else {
        // 系统关闭暗色模式后做什么
      }
    });

# 兼容性

其实 `prefers-color-scheme` 也不是什么新东西了，去年水果就已经在 macOS Mojave 上的 Safari 中添加了它，随后各浏览器不断跟进，现在的兼容性还算不错：

[![](/pictures/NsaMSv5pkiThG7Z.png)](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-color-scheme#Browser_compatibility)

# 扯点不相关的

> “为什么没有夜间模式？你的夜晚太珍贵，我们不忍心占用，更不愿意成为你半夜醒来看手机的原因。愿你每夜好眠。”
>——微信团队张某



> “为了优化用户体验，微信与苹果达成了合作，共同探索微信在 iOS 系统的暗黑模式体验，目前该功能已完成开发，将有望在下一个新版本中上线，敬请期待。”
>——微信团队张某

只有水果配教你做产品？
