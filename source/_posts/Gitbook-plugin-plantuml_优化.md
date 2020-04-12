---
title: Gitbook-plugin-plantuml 优化
date: 2018-9-23 14:37:30
categories: 小工具
tags: [plantuml, GitBook, plugin]
blog: true
---



我的笔记都是使用 Markdown 编写的，并且使用 GitBook 搭建一个本地 Web 网页，可以像读书本一样看自己的笔记。

在写笔记的过程中，可以直接打开网页查看效果，特别方便，更重要的是 GitBook 自带 PlantUml 插件，可以直接写 uml 代码。

但是呢，使用过程中，发现 PlantUml 插件是有问题的。修改 uml 代码之后，点击保存，等待4，5秒，再次点击保存，才能在网页看到 UML 的修改效果。看了 Gitbook-plugin-plantuml 的源代码，发现工作原理很简单。

<!-- more -->

1. 先用正则查找每一个文件中所有 "\`\`\`uml" 和 "\`\`\`" 之间的内容，并放在一个文件中
2. 计算这个文件的 md5 用于下一次文件改动时，检查文件是否改变
3. 使用 plantuml 生成 png 图片保存到 assets/images/uml/ 目录下
4. 下一次保存文件时，会把 assets/ 目录下的文件拷贝到 _book/ 目录下

这个插件的 Github 地址为 https://github.com/lyhcode/gitbook-plugin-plantuml ，在 issues 中已经有人提 issue 了， 1 年了，还没有解决。然后找了一个修改版，这个修改版已经解决了保存两次才能生效的问题，但是还有一个不足，每次保存，会把所有 UML 文件全部重新生成 png 一次，一次生成几十张，CPU 风扇呼呼呼地叫，特别不舒服。于是对其进行优化。

修改的内容为，新建一个缓存目录，如果 uml 文件没有改动，就不会重新生成 png 图片，而是把缓存的图片复制到 _book 目录下，修改的 uml 会生成新的 png 图片，并且把已经废弃的图片删除。

我修改过后的插件仓库地址为 https://github.com/binwin20/gitbook-plugin-plantuml 

使用插件

```json
{
    "plugins": [
        "plantuml@git+https://github.com/binwin20/gitbook-plugin-plantuml.git"
    ]
}
```

如果在这之前已经使用了官方 gitbook-plugin-plantuml 插件，需要把 assets/images/uml 下的所有内容全部删掉。然后运行 `gitbook install` 重新下载插件

