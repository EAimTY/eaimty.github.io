+++
title = "关于 Android 10"
date = 2019-09-11

[extra.comments]
issue_id = 32

[[extra.comments.addition]]
user = { login = "ホロ", html_url = "https://blog.yoitsu.moe", avatar_url = "https://secure.gravatar.com/avatar/13650e6abb26b891c6bed92ffe8b2b5f?d=retro" }
created_at = "2019-09-13T12:01:12Z"
body = "立刻关机？ 😂😂"

[[extra.comments.addition]]
user = { login = "EAimTY", html_url = "https://www.eaimty.com/", avatar_url = "https://secure.gravatar.com/avatar/f64037e002ad7b01fe9ee60351bfd1d2?d=retro" }
created_at = "2019-09-15T08:08:09Z"
body = "> 立刻关机？ 😂😂\n\n关机❌砸手机✔️\n不过最近我的手机真的摔了，我这辈子第一次摔手机就把背面玻璃摔裂了…😭"

[[extra.comments.addition]]
user = { login = "Benny", html_url = "https://www.bennythink.com", avatar_url = "https://secure.gravatar.com/avatar/38a94d1b56f913b2531ab3a36577d3e4?d=retro" }
created_at = "2019-09-11T12:59:20Z"
body = "觉得这个手势很智障……"
+++

从 9 月 3 号 Android 10 正式发布到现在已经一个星期了。写篇文章说说我在这一个星期的使用后对新系统感觉，顺便聊聊我现在对 Android 本身的看法。

<!--more-->

这篇文章不是介绍 Android 新 Feature 的文章，那类文章网上一搜一大把，况且我的水平也肯定不如专业人士，再写一遍就是费力不讨好了。

以下内容都是基于我个人的使用习惯与看法得出的，可能会和一些人的观点冲突。

# 不错的改进

## Scoped Storage

Android Q Beta 3 去掉 sandbox 时人们哭声一片，本来看到了镇住流氓的希望，这一下子又回到了解放前。
但仔细想想，毕竟生态有历史遗留问题，Google 像水果一样强推某项功能很难。
取代 sandbox 的就是 Scoped Storage，虽然显得不那么积极，就像 Storage Access Framework 一样，但这也是无奈之举。
当前 Scoped Storage 秉承了 Google 一贯的“防君子不防小人”政策，但同时也承诺在明年的 Android R 中会强制对所有 app 开启，乐观一点的话，希望到明年应用在存储里到处拉屎的现象会少见一点吧。

## 随机传入的 MAC 地址与可控制的 READ_PRIVILEGED_PHONE_STATE 权限

简单来说，就是应用无法再获取用户唯一标识符了，`getWifiMacAddress()` 这个方法是设备管理员专用，`/proc/net` 也被限制访问，另外只有拥有`READ_PRIVILEGED_PHONE_STATE` 权限的 app 才能读取 IMEI。
剩下的方式中，Advertising ID 可以由用户改变，建立文件存储 ID 这种方法对我这种经常清理存储中文件的用户来说也没什么用。

## 其它对应用的限制

新系统中，剪切板被限制只能由输入法和焦点程序访问、应用开关 WiFi 也被限制。
总的来说，在隐私管控方面，Google 还是做了一些有意义的事。

## 新 Gesture navigation 下的 Google Assistant 唤出方式

虽然我对新Gesture navigation感觉一般，但说实话，新的Assistant唤出方式我还是很喜欢的，虽然改变这么多年长按home键的习惯不那么容易，但是这新的手势会给人一种更加灵动的感觉。

## 状态栏下拉菜单中可直接开启暗色模式

这个功能本身很不错，但问题是Google自家的app都还没完全适配，更别说第三方app了。如果后续有更多应用适配，我想体验会很不错。

好话说完了，下面说说缺点。

# 被做臭的地方

## 新的手势系统

我个人认为这是新系统最大的槽点。

首先是应用间的切换，假如你正在同时使用 3 个应用，想从 1 切换到 3，新手势不像之前的 2-button navigation 一样给人感觉一气呵成，另外想进入最近任务列表也需要停顿一下，不如之前的方便。

其次就是 Google 这次最大的败笔：新手势的返回和拉出 drawer 操作冲突，只有把手势灵敏度调成最低才能勉强拉出。

drawer 是 Material Design 里最基础、最普及的一个组件，Google 你干脆把 MD 弄死算了

## 设置中便捷切换 WiFi、蓝牙的组件

Google 这是在自己填自己挖的坑。

Android L/M 时代，状态栏中就有二级菜单可以切换 WiFi、蓝牙，结果这个功能在 Android N 中被取消了。现在在设置 app 中加入这个组件聊胜于无，用户早就习惯在状态栏长按对应 icon 进入设置了，谁还会专门打开设置 app？

## “Choose use which app to open”中的“Always use this app”需要到应用设置中设置

这不是脱了裤子放屁吗？我根本无法理解这个改变是为了什么，设置完你还需要重新点击想要打开的内容，假如你想打开的内容显示时间有限，那该怎么办？

## 各种各样的 bug

- 开启 AOD（Always on Display）后，最下面一行的电量信息和“Device is managed by your organization”只能显示一半；
- 刚刚更新完后 play 报`DF-DFERH-01`错、重启、清除应用设置 N 次也没有解决，第二天正在考虑退回 Android P 时又自己恢复正常了；
- play 中已购买的 app 要求重新付费，吓了我一跳，还以为我的 Google 账号出了问题，之前买过的一堆解锁器和游戏难道都飞了？还好和上一条一样，第二天又自己恢复正常了；
- 最近任务列表中应用预览经常性地和实际应用对不上号，就是你打开 photos，进入最近任务列表后发现预览竟然是 calendar...
- 点击最近任务列表中的应用有时会显示 App isn't available。
- ...

# 在不 root 的情况下放心使用手机

在更新系统前，我一直以为新的 sandbox 功能还在，而之前的消息称只有在Q下新安装的app才会启用sandbox，所以我索性就直接下载了factory image，干脆彻底重装吧。

结果更新完、刷完 twrp 后才发现，sandbox 早就被移除了，Android Q 的加密也改了，twrp 无法 decrypt data 分区，sideload 也坏了...
既然刷不了 magisk，那干脆 twrp 也不要了，直接把 bootloader 锁上算了，更安全嘛。

这么多年来，我还没有尝试过在没有 root 的情况下使用手机，但这次发现无 root 其实没有我想象的那么恐怖。

移动网络、WiFi 上的`×`号可以通过 adb 去掉；把 island 通过 adb 设置为 Device Manager 后可以接管 Mainland，从而 App Ops、Ice Box 都可以使用了，唯有 Greenify 在不 root 的情况下比较麻烦，休眠应用需要借助系统应用列表中的 force stop。

Google Now Feed 和 Google Location Report/History 就比较麻烦了，一番搜索后，找到了推友李先生的两篇文章：

- [免 Root 修复你的 Google Now Feed](https://plumz.me/archives/8889/)

- [消除 Google Phone APP 的权限提示](https://plumz.me/archives/9884/)

按照文章中的方法可以开启 Google Now Feed 和 Google Location Report/History，但问题是经常会弹出各种各样的错误通知：XXX 应用需要 play service 开启 phone 权限，很烦。

# 说说对 Android 的看法

你可曾记得 Android M？

从 L 升级到 M，UI 上的变化不多，但正是对功能上的小修小补，造就了我个人认为最好的一代 Android。M 的 UI 在今天看来可能都不算落伍；

那时的 Material Design 还没有 Bottom Bar，整个系统简洁、美观；
Now on Tap 的 OCR 还非常方便、好用；
新加入的应用权限控制功能也不错，虽然有“亲爱的用户，我是你爹”式的 app，但是用 app ops 也可以管住它们；
我们还可以用 substratum 改各种主题；
Google 的口号还是“Don't be evil”而不是“Do the *right* thing”
...

回看这 5 年来 Android 的更新，可以看到 Google 在创新，同时也不在创新。

每次更新，Google 都会带来一套新的 UI，且不说它有没有起到方便用户的作用，增加用户学习成本倒是完美做到了。
然而在权限管控方面，Google 永远做得那么不紧不慢。应用沙盒，Google 搞了这么多年也没搞完；加入了权限开关，结果想不到这个锁竟然如此“君子”，竟然会出现“不给权限不让用”这种情况？

当然，由于历史遗留问题，手段强硬地加入新政策、新功能短时间内会让厂商、用户很“痛”，但长“痛”不如短“痛”。可惜，因为 Google 的不作为，我们这些用户一直在“痛”。

我们什么时候才能再收到一次像 Jelly Beam -> KitKat 或 KitKat -> Lollipop 一样的更新？

或许是 Google 变了。

从非 Chrome 浏览器浏览 YouTube 时功能被限制，到 Dragonfly 项目，到 Google Home 用户录音被工作人员收听，再到限制 Chrome 中的 adblock 插件，Google 已经不再是我们熟悉的那个 Google 了。

或许，是时候与 Google 说再见了？
