+++
title = "关于 Typecho 的小技巧"
date = 2016-01-06

[extra.comments]
issue_id = 3
+++

最近，架不住一个 [逗 B](https://www.twznow.com/) 的频频安利，花了些时间把博客平台换到了 Typecho。
面对一个陌生的程序，我刚开始一脸懵逼...

不过用过一段时间后，还是找到了一些使用姿势。

<!--more-->

# 让评论支持Markdown

Typecho 的一大特点就是原生支持 Markdown，这也成了很多人选择它的理由。但是它的评论功能默认没有开启 Markdown 的支持，那我们应该如何开启呢？

1. 首先，需要在 Typecho 后台的`评论设置`中勾选`在评论中使用Markdown语法`。
2. 在`允许使用的HTML标签和属性`中输入：

`<a href=""> <blockquote> <code> <del> <em> <h1> <h2> <h3> <h4> <h5> <h6> <hr> <img src=""> <li> <pre> <strong> <table> <td> <tr> <ul>`

这段标记允许让 Typecho 将 Markdown 标记转换为对应的 HTML 标签。

# 让Typecho支持emoji

开始用 Typecho 的时候，写第一篇文章时按照在 WordPress 上的习惯用了几个 emoji，但发表后发现...怎么全没了...😓
最后发现，原来是数据库的问题。WordPress 默认的数据库编码是 utf8mb4，而 Typecho 是 utf8，也就只支持 3 个字节。想要让 Typecho 支持 emoji 表情，就需要将 Typecho 数据库的编码改为 utf8mb4。

首先通过 phpMyAdmin 或在命令行下修改数据库的编码为 utf8mb4：

```sql
alter table typecho_comments convert to character set utf8mb4 collate utf8mb4_unicode_ci;
alter table typecho_contents convert to character set utf8mb4 collate utf8mb4_unicode_ci;
alter table typecho_fields convert to character set utf8mb4 collate utf8mb4_unicode_ci;
alter table typecho_metas convert to character set utf8mb4 collate utf8mb4_unicode_ci;
alter table typecho_options convert to character set utf8mb4 collate utf8mb4_unicode_ci;
alter table typecho_relationships convert to character set utf8mb4 collate utf8mb4_unicode_ci;
alter table typecho_users convert to character set utf8mb4 collate utf8mb4_unicode_ci;
```

然后修改 config.inc.php 中的数据库编码设置：

```php
$db->addServer(array (
  'host'      =>  localhost,
  'user'      =>  'blog',
  'password'  =>  'password',
  'charset'   =>  'utf8mb4', //修改这里
  'port'      =>  3306,
  'database'  =>  'blog'
), Typecho_Db::READ | Typecho_Db::WRITE);
```

好了，大功告成！

P.S. 很多人觉得 Typecho 的 Markdown 有问题，例如写标题时用 `##文字` 没有效果。其实，标准的 Markdown 语法是 `## 文字`，`#` 与 `文字` 之间是有一个空格的。

***

本文参考了：
[Typecho评论中开启和使用Markdown的方法- TypeCodes](https://typecodes.com/mix/typechocommentmarkdown.html)
[使 Typecho 支持 emoji 的显示😂😂😂 - Hran 的博客](https://get233.com/archives/show-emoji-in-typecho.html)
