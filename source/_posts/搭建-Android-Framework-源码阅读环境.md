---
title: 搭建 Android Framework 源码阅读环境
date: 2017-02-24 23:18:30
tags: [Sublime, 源码]
categories: 源码阅读
blog: true
---



电脑磁盘太小，放不下整个 Android 源码。
以前要看代码都是把Android源代码下载到移动硬盘中，然后用 Sublime 或者 Android Studio 看。
每次看都要插硬盘，看完之后要推出硬盘，真是麻烦。

后面在网上找到了在线的 Android 源码 [Androidxref][]，可以搜索代码，跳转等。在这里看代码真心不错，如果网络好的话，但是有时候网络不好，速度太慢，甚至打不开，不能忍。

现在想想，对于我这个渣渣，又不是搞 Framework 开发的，更不是系统开发的，要整个 Android 源码有啥用？只要 Framework 代码就够了，偶尔可能还会看一下 Java 代码，再加一个 libcore 代码就足够了。接下来就简单了。

<!-- more -->

把 libcore 和 frameworks 目录拷贝到电脑，然后用 Sublime 打开，使用 CTag 建立索引，就可以看代码了。

看代码的时候经常要跳转，
command + 鼠标左键跳转到方法定义，
ctrl + 减号回退到上一次光标位置，
ctrl + shift + 减号返回前进到上一次光标位置。也可以自定义

Preferences -> Key Bindings 在 User 配置中添加:

```json
[
    { "keys": ["ctrl+alt+left"], "command": "jump_back" },
    { "keys": ["ctrl+alt++right"], "command": "jump_forward" }
]
```

就可以 ctrl+alt+left 返回 ctrl+alt++right 前进了。

[Androidxref]: http://androidxref.com/
