---
title: Sublime Text 3 编译并运行 Kotlin
date: 2018-02-08 21:48:25
tags: [Kotlin, Sublime Text 3]
categories: 小工具
blog: true
---



Kotlin 是一门年轻的语言，由 JetBrains 开源，去年 Android 已经把 kotlin 作为官方语言了，可以在 JetBrains 和 Android Studio 中编写和运行 kotlin 语言。但是对于对于初学者，使用这两个工具练习 kotlin，真是大材小用，于是找了个轻量级的，自己熟悉的 Sublime Text 来编写和运行 kotlin。

首先，需要配置编译环境， Tools > Build System > New Build System，复制下面内容，保存名称为 kotlin_cmd.sublime-build

```json
{  
    "cmd": ["kotlinc $file_name -include-runtime -d ${file_base_name}.jar && java -jar ${file_base_name}.jar && rm ${file_base_name}.jar"],  
    "working_dir": "${file_path}",
    "selector": "source.kotlin",  
    "shell": true  
}
```

首先通过 kotlinc 编译出 jar 文件，然后使用 java 运行 jar 文件，最后删除 jar 文件，不留下任何垃圾。

新建一个 kotlin 文件 Test.kt, 编写代码

```kotlin
fun main(args: Array<String>) {
   println("hello Justin Wu!")
}
```

选择编译工具 Tools > Build System > kotlin_cmd

编译并运行代码 Tools > Build 或者使用快捷键 `command + B`

References:

1. <https://kotlinlang.org/docs/tutorials/command-line.html>
2. <http://www.cnblogs.com/final/p/5348350.html>