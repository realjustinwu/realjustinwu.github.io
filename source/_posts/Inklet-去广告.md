---
title: Inklet 去广告
date: 2017-07-25 22:14:52
tags: [Inklet, SketchBook, Alfred, shell]
categories: 小玩意
blog: true
---



最近发现一个把触摸板当画板的小软件 —— [Inklet](https://tenonedesign.com/inklet.php)。

这是一个收费软件，也可以免费使用，但是每隔200s左右会有25s的广告时间。

{% img "/images/inklet-snapshot.png" %}

我真是太穷了，买不起正版，但是作为一个有一点点道德的程序员，也不能去破解同行的软件的（其实是不会破解）

广告是很烦的东西，我怎么可能把宝贵的时间浪费在这里呢？于是，每次快要出现广告的时候我都会关闭 Inklet 然后重启，就可以跳过广告了。写了一个快速重启的脚本。

新建文件 inkletRestart
```
killall Inklet
open -a  /Applications/Inklet.app/Contents/MacOS/Inklet 
exit
```

添加可执行权限

```bash
sudo chmod u+x inkletRestart
```

然后每次双击这个文件就可以重启了

如果安装了 Alfred 的话，用 open 命令打开这个文件: `空格 + inkletRestart`






