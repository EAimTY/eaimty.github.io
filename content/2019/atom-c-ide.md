+++
title = "将 Atom 改造为一个简单的 C/C++ IDE"
date = 2019-10-06

[extra.comments]
issue_id = 33

[[extra.comments.addition]]
user = { login = "EAimTY", html_url = "https://www.eaimty.com/", avatar_url = "https://secure.gravatar.com/avatar/f64037e002ad7b01fe9ee60351bfd1d2?d=retro" }
created_at = "2020-02-08T19:04:13Z"
body = "> 大佬，请问在装了这些插件之后，应该怎么实现同时编译多个文件？\n\n“同时编译多个文件”指的是什么？可能你需要的是cmake，atom有个配套的插件`build-cmake`你可以试一试。\n另外还有[Atom IDE](https://ide.atom.io)可以提供类似IDE的功能。"

[[extra.comments.addition]]
user = { login = "Erhecy", html_url = "https://www.mmcee.cn/", avatar_url = "https://secure.gravatar.com/avatar/60d1ac1d0a5f4dc9113b1740632e9fc6?d=retro" }
created_at = "2020-02-11T08:53:38Z"
body = "> 用着 vscode 写 C++ 的路过\n\n同主楼"

[[extra.comments.addition]]
user = { login = "嘿嘿嘿", avatar_url = "https://secure.gravatar.com/avatar/60704096935c4f6f73bd6c7c862c4c2b?d=retro" }
created_at = "2021-01-27T05:46:31Z"
body = "> dalaotql\n\n来看看\n到底怎么样"

[[extra.comments.addition]]
user = { login = "vifly", html_url = "https://viflythink.com/", avatar_url = "https://secure.gravatar.com/avatar/7e9bb79461a5b4f65d3ef042fb639d36?d=retro" }
created_at = "2019-10-13T11:29:06Z"
body = "用着 vscode 写 C++ 的路过"

[[extra.comments.addition]]
user = { login = "源氏不想拔刀", avatar_url = "https://secure.gravatar.com/avatar/c844efcdcba14e8399bb29c7728a41d7?d=retro" }
created_at = "2020-02-08T12:10:09Z"
body = "大佬，请问在装了这些插件之后，应该怎么实现同时编译多个文件？"

[[extra.comments.addition]]
user = { login = "源氏不想拔刀", avatar_url = "https://secure.gravatar.com/avatar/c844efcdcba14e8399bb29c7728a41d7?d=retro" }
created_at = "2020-02-10T12:05:25Z"
body = "> > 大佬，请问在装了这些插件之后，应该怎么实现同时编译多个文件？\n> \n> “同时编译多个文件”指的是什么？可能你需要的是cmake，atom有个配套的插件`build-cmake`你可以试一试。\n> 另外还有[Atom IDE](https://ide.atom.io)可以提供类似IDE的功能。\n\n感谢大佬！我研究研究！"

[[extra.comments.addition]]
user = { login = "乌黑密发", avatar_url = "https://secure.gravatar.com/avatar/f57cf3894423cfba407f469042bbdb91?d=retro" }
created_at = "2019-10-07T09:07:07Z"
body = "dalaotql"
+++

如果你符合以下这些特点，这篇文章会很有用：

- 单纯喜欢 Atom 的人
- 单纯觉得 VS 难用的人
- 有洁癖，不愿使用闭源软件的人
- 讨厌微软的人
- 喜欢折腾的人（其实折腾 Atom 也不是非常难）

当然，即使你不属于上面这几类人，也应该体验一下使用 Atom 的感觉，因为只有对比过后才能知道哪个更适合自己。

最近在学校修 cpp 课程，授课老师要求用 VS，然而我翻了一下教材，发现其实没有用 VS 的必要，所以准备把我一直在用的 Atom 改造为一个 cpp 的 IDE。

成品可以实现 C/C++ 程序的实时检查代码中的错误，可以一键编译，还可以和 VS 一样用图形化界面打断点、逐行运行来 Debug。

<!--more-->

Atom 有如下几个有点，让它比 VS 高到不知道哪里去：

- 开源，且跨平台，这意味着如果你是一个使用 Atom 的 Linuxer，在某些情况下被迫要用 Windows，你可以很简单地打包一份相同配置的 Windows 版 Atom，即插即用
- 高度可定制化，每个人的 Atom 都可以完全不一样
- 原生高度集成图形化的 Git
- 基于 Electron，运用了大量 Web 技术，原生对前端编写的支持优秀
- 极度模块化，真正的 Atom 只是一个用来实现插件功能的核心，没有任何功能，全部功能都是由插件提供的
- 庞大的社区，数不胜数的插件，与上一条搭配意味着 Atom 的改造难度极低，这让 Atom 可以轻松充当多面手，不只前端，C 家族、Python 等众多语言的 IDE 也可胜任，同时它还是一个优秀的 Markdown 编辑器。
- 自从某次更新后，Atom 的运行速度有了很大的改善。到现在，Atom 已经是一个轻快的编辑器了。

什么？你说 VS Code？那不就是微软给 Atom 披了个马甲吗？

# 准备原材料

即使不考虑 Atom，想要编译、Debug C/C++ 程序，首选也一定是 gcc、g++ 和 gdb，所以我们要做的就是把 Atom 和上面三个程序结合起来，结合的媒介就是 Atom 的插件。

## 安装 Atom

对于 Unix-Like 系统，一般发行版的仓库都会有 atom，就算没有，也可以去[项目的 Releases 页](https://github.com/atom/atom/releases)下载对应版本，甚至可以直接下载打包好的压缩包，解压后运行主程序文件。

对于 Windows，你可以选择去[项目的 Releases 页](https://github.com/atom/atom/releases)下载对应的 exe 安装包，但安装过程中你无法选择安装路径，所以我一般会下载 zip 包直接解压使用。

## 安装 gcc、g++ 和 gdb

对于 Unix-Like，不用多说，大家都会。

对于 Windows，这里要用到 [MinGW](http://www.mingw.org)，这是一个 Windows 下 GNU 家软件的安装器，使用起来非常方便。我们需要的是 mingw32-gcc-bin、mingw32-gcc-g++-bin 和 mingw32-gdb-bin 这三个软件包，其它依赖会自动下载。
MinGW 的食用方法网上一搜一大把，我就不在赘述了。
注意 MinGW 只提供 32 位版的软件包，如果你有什么特殊需求或者是强迫症必须要 64 位版本的软件，可以使用 [Mingw-w64](http://mingw-w64.org)，道理都是一样的。

在安装过 gcc、g++、gdb 后，需要将 MinGW 安装目录下的 bin 文件夹（里面有一大堆可执行文件）添加到 Windows 的环境变量 `PATH` 中。环境变量的添加方法网上也有一大堆，不再重复。
添加到 `PATH` 后需要重启 Windows 以使做过的更改生效。

# 安装插件

下面的步骤都是所有平台通用的。

进入 Atom 的设置，进入插件查找安装选项卡，装上以下几个插件：

- linter
- linter-gcc2（原版 linter-gcc 停止开发了，这是另一位开发者接手后的版本）
- gcc-make-run
- dbg
- dbg-gdb

确保以上插件全部安装成功后重启 Atom，会弹出一些窗口让你安装这些插件的依赖，全部安装即可。

# 设置插件

打开插件管理面板，这里能看到你安装过的所有插件。

首先是 linter-gcc2 插件
需要在它的设置中打开 Lint on-the-fly，这是实时查错功能。

之后是 gcc-make-run
需要在它的设置中的编译参数（Compiler Flags）里增加`-g`参数，以使 gdb 可以正常 debug 编译好的 binary 文件。

在这里，对于 Linux 用户还有一步额外的操作。在 Linux 下，gcc-make-run 默认调用的终端是 xterm。我相信很大一部分 Linuxer 们都没有装这个又老又丑的终端，即使装了也大概率是因为某些依赖问题，不会日常使用。所以我们需要修改默认调用的终端。
gcc-make-run 插件设置中的最后一项 `Terminal Start Command (only Linux platform)` 就是自定义终端调用命令，你可以查看你使用的终端的帮助或者问问“好男人” (man) 来自行设置调用命令。注意要用 bash 的 `-c` 参数来使程序执行完后终端保持打开状态。
举个例子，我用的是 xfce4-terminal：`xfce4-terminal -T $title -x bash -c`

***

以上就是改造的步骤，下面说一说如何使用。

# 快捷键

在这套配置中，默认下，F6 键是编译并运行，F5 键是打开 Debug 窗口，F9 键是对选中的行打断点。

# 如何 Debug？

首先，你要保证代码没有被 linter 查出错误，这是最基本的；
然后对着你想打断点的行的行数左侧点一下，出现的红色按钮就是断点标识，用 F9 键也是同样的效果；
之后，按下 F6 键，也就是编译出代码的可执行文件；
紧接着在左侧 Tree View 中右键点击编译好的二进制文件，选择 Debug this File；
最后点击右下角的 Debug 按钮，就能开始 Debug 了。

同时，dbg 插件还可以保存断点之类的调试信息，按钮就在 Debug 按钮的下方。

# 最后推荐几个 Atom 插件

**minimap**

可以说是 Atom 的必装插件之一，提供整个文件的缩略图预览。

**autocomplete-clang**

依赖于 autocomplete-plus（atom 自带），且系统中需要有 clang 软件包。
可以提供非常强大的自动补全功能。

**autocomplete-paths**

顾名思义，是一个自动补全 path 的插件，也依赖于 autocomplete-plus。

**atom-beautify**

只需按一下快捷键，自动规范格式化你的代码。
Beautify C 语言依赖于 Uncrustify，但安装配置起来很简单。

**activate-power-mode**

越写越爽

**atom-miku**

（引述原作者的话：）
> - 做工粗糙
> - 仅供娱乐
> - 没有卵用

（逃
