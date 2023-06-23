+++
title = "在 Nginx 的 if 条件中使用“与”、“或”"
date = 2018-03-24

[extra.comments]
issue_id = 26

[[extra.comments.addition]]
user = { login = "老李", avatar_url = "https://secure.gravatar.com/avatar/bb26f0adac7706d47cd9b41a4578a081?d=retro" }
created_at = "2018-04-19T06:53:03Z"
body = "我也觉得"

[[extra.comments.addition]]
user = { login = "卢本伟", avatar_url = "https://secure.gravatar.com/avatar/18b9d219b84123997366291c235471da?d=retro" }
created_at = "2018-04-10T06:25:36Z"
body = "备案略屌"

[[extra.comments.addition]]
user = { login = "三七", avatar_url = "https://secure.gravatar.com/avatar/3300499b9f34be851551053d748a2021?d=retro" }
created_at = "2020-12-12T12:36:44Z"
body = "学到了，还有判断nginx字段为空的方法$referer ~* \"^$"
+++

相比与 Apache，Nginx 的 config 写起来更舒服一些。

但是，很多情况下，我们的需求可能会稍稍有些复杂，例如在一个判断语句中使用多个条件。Apache 写这种判断很简单，但使用 Nginx 该如何实现呢？

<!--more-->

Nginx 中的 if/else 的语法与大多数语言一致：

```conf
if (<condition>) {
    # Do something
} else {
    # Do something
}
```

虽然 Nginx 并非编程语言，我们还是先来试试在其它语言中常用的手段吧。

# 试错

## 使用逻辑运算符

```conf
if (<condition1> AND <condition2>) {
    # Do something
}
```

用 `nginx -t` 测试一下，报错...

试试用符号？

```conf
if (<condition1> && <condition2>) {
    # Do something
}
```

还是有问题，看来 Nginx 并不支持逻辑运算符。

## 使用多个 if 块嵌套

试试“曲线救国”：

```conf
if (<condition1>) {
    if (<condition2>) {
        # Do something
    }
}
```

`nginx -t`，还是报错...

# 正确的姿势

在网上找了一些文章，终于解决了这个问题。

关键就在于：用变量存储状态。

## “或”

```conf
set $foo 0;
if (<condition1>) {
    set $foo 1;
}
if (<condition2>) {
    set $foo 1;
}
if ($foo = 1) {
    # Do something
}
```

只要两个条件中有任意一个成立，$foo 就会被赋值为 1，最后一个代码块就会被执行。

另外，如果“或”用在判断的变量的值时，可以用 `|` 更简单地解决：

```conf
if ($letter ~ ("A"|"B"|"C"|"D")) {
    # Do something
}
```

但需要注意的是，不能这样写：

```conf
if ($letter ~ ("A"|"B") | $letter ~ ("C"|"D")) {
    # Do something else
}
```

## “与”

```conf
set $foo "";
if (<condition1>) {
    set "${foo}1";
}
if (<condition2>) {
    set "${foo}1";
}
if ($foo ~* "11") {
    # Do something
}
```

首先把 $foo 清空，之后每当有一个条件成立，就会在 $foo 的结尾添加一个 1，当 $foo 中的字符串为“11”时，最后的代码块会被执行。

## 更多个条件组合

```conf
set $foo "";
if (<condition1>) {
    set $foo "1";
} else {
    set $foo "0";
}
if (<condition2>) {
    set $foo "${foo}1";
} else {
    set $foo "${foo}0";
}
if ( $foo ~* "011" ) {
    # Do something
}
```

开始时 $foo 是空字符串，每当一个判断成立时 $foo 的最后会被添加一个 1，不成立则添加 0，以此类推，最后一个 if 块中 $foo 要匹配的字符串就是你希望实现的判断。

Nginx 的变量字符匹配中，`~*` 代表匹配，`!~*` 代表不匹配（实际上都是正则）

# 说了这么多，这些能干什么？

举个例子，当我们直接用 Nginx 给 Google Analytics 发送数据时，一般需要考虑两个条件：

- 是否是爬虫
- 用户是否开启 DNT（Do Not Track）

这时多条件判断就派上用场了。

类似的使用环境有很多。
