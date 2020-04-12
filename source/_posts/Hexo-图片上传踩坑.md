---
title: Hexo 图片上传踩坑
date: 2016-08-28 00:05:27
tags: [Hexo]
categories: 其他
blog: true
---



一直都使用 DropBox 保存笔记，搭建博客的时候也想着要把重要的东西放到 DropBox 中。于是把 项目中 _config.yml 的 source_dir 指向到 DropBox 的文件夹中。写第一篇博客没发现问题，写第二篇博客也没发现问题。因为没有使用图片，写第三篇博客 [ActiveAndroid 源码学习][] 的时候需要用到图片，但是图片不显示。。。开始了 debug 之路。

<!-- more -->

于是找谷歌，大概有两种方式：一种是使用第三方图床，另一种是使用本地保存。于是尝试使用七牛图床，我的图片都放在一个文件夹中的，直接把这个文件夹同步到七牛，一个脚本处理所有的图片链接，简直太方便了。于是开通七牛，实名认证，域名绑定的时候，要求域名备案，域名备案，备案。。。

然后看到 [在 hexo 中优雅的使用本地图片][] 这篇博客，这里介绍了两种放图片的方式，一种放在根目录（我要的就是这种），另一种是建立一个跟文件名相同名称的目录，然后使用相对路径就能访问。两种都试过了，都没有效果。打开 public 文件夹，在 images 目录下和文章同级目录下都没有找到图片文件。为啥别人能用自己用不了呢？

首先想到的是，是不是所有的图片都有问题，打开 public/images 目录，发现其中有一些图片包括 gif, jpg, svg 格式的。然后找找这些图片的来源，都是从 [Next][] 主题中复制来的。把自己的图片放这个主题中，显然是可以的，但是跟主题混在一起，以后更改主题图片就又出问题了，这种处理方式显然不对。。再找找有没有其他的没被 Hexo 处理的文件。只有 vendors 这个文件夹是没有被处理的，但是这个文件夹也是从主题复制来的。忽略。。

谷歌啥图片没有拷贝到 public 目录中，无解。只能自己尝试了。

首先做的是把 source_dir 改回初始化的值 `source`，并且在 source/, source/_post/, source/images 文件夹下都放不同的图片。运行 `hexo g`, 查看 public 目录，发现 source/, source/images 目录下的文件都能复制到 public 中。为啥在 DropBox 中的一个都不能复制呢？难道是权限问题？

于是 `ls -al`, 结果如下：
Hexo 中的文件和文件夹

```bash
drwxr-xr-x   3 binwin20  staff   102  8 27 16:21 images
-rw-r--r--@ 1 binwin20  staff  54092  8 27 10:58 6F869754-243C-47A9-B9A4-A2031948D20A.png
```

DropBox 中的文件和文件夹

```bash
drwxr-xr-x@  4 binwin20  staff    136  8 27 11:47 images
-rw-r--r--@  1 binwin20  staff  54092  8 27 10:58 6F869754-243C-47A9-B9A4-A2031948D20A.png
```

文件夹多了一个 `@`，`@` 是啥呢。谷歌一下：它包含了文件的扩展属性。还是不明白，管他呢，把 DropBox 中的复制到 Hexo 中：

```bash
drwxr-xr-x@  4 binwin20  staff   136  8 27 11:47 images
```

Hexo 中的图片还是能生到public 中，显然不是 `@` 和权限导致的问题。

于是尝试把 source 放在上一层目录中（跟 Hexo 项目同级）。运行 `hexo clean`, `hexo g`，图片不能复制。。。

然后尝试把路径改成绝对路径，source 名称改成 source1，都试了一下。发现只有 source 在 Hexo 文件夹下才能把其中的图片复制到 public 文件夹下。

猜想应该是 source_dir 的问题。于是全局查找 source_dir。

load_config.js 中：  `ctx.source_dir = pathFn.resolve(baseDir, config.source_dir) + sep;` 没有具体的使用，名称依旧是 source_dir，忽略，继续查找

/hexo/lib/hexo/post.js 中:  `var draftDir = pathFn.join(ctx.source_dir, '_drafts');` 在这里定义了 draftDir，肯定有使用。但是 draft 是草稿的意思。直接忽略，还不如看 post 呢。

source.js 几行代码，可惜都看不懂。

跟 source_dir 有关的代码都看了一下，然并软。看不懂 js 代码，不知道执行流程。然后想到 hexo 有一个 `--debug` 模式，于是执行 `hexo clean`, `hexo g --debug`,

当 source_dir 指向 DropBox 中时

```bash
...
Loading database.
01:34:17.750 INFO  Start processing
01:34:17.787 DEBUG Processed: favicon.ico
01:34:17.820 DEBUG Processed: images/6F869754-243C-47A9-B9A4-A2031948D20A.png
01:34:17.880 DEBUG Theme config loaded.
...
```

当 source_dir 指向 Hexo 中时

```bash
...
Loading database.
01:34:17.750 INFO  Start processing
01:34:17.787 DEBUG Processed: favicon.ico
01:34:17.820 DEBUG Processed: images/6F869754-243C-47A9-B9A4-A2031948D20A.png
01:34:17.880 DEBUG Theme config loaded.
...
01:34:20.621 INFO  Generated: about/index.html
01:34:20.625 INFO  Generated: images/6F869754-243C-47A9-B9A4-A2031948D20A.png
01:34:20.625 INFO  Generated: images/avatar.jpg
...
```

于是全局查找 `Start processing` 和 `Processed:`，还是看不懂 js 代码。

再次谷歌 `hexo source_dir` 搜到 github 中别人提的一个 [issue][]，没有人回答。再看看提 Issue 的人[Evan You][] **Creator of @vuejs, previously @meteor & @google**，哇！Vuejs！我们现在外包给别人做的 H5 页面就是使用 Vuejs 做的。

不能把 source_dir 指向的其他的文件夹，定义这个 source_dir 有软用。不懂 js 😪

在这里死扣也不是办法，于是谷歌 DropBox 同步多文件夹，还真有 [链接][DropBox 同步多文件夹]

以后有时间学学 Js 和 ReactNative。

测试一下上传图片：

{% img "/images/testimage.gif" %}

[在 hexo 中优雅的使用本地图片]: https://codefalling.com/2015/12/19/no-pains-with-hexo-local-image/
[Next]: https://github.com/iissnan/hexo-theme-next
[issue]: https://github.com/hexojs/hexo/issues/1666
[Evan You]: https://github.com/yyx990803
[DropBox 同步多文件夹]: http://www.linuxidc.com/Linux/2011-05/35373.htm
