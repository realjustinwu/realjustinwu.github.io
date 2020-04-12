---
title: 依赖包不更新的 bug
date: 2018-6-28 07:30:52
categories: Android
tags: [bug, 依赖包]
blog: true
---



### bug 描述

build.gradle 文件中依赖包改成了新的，但是侧边栏 Project 的 External Libraries 中使用的依旧是老的依赖包。不管 Rebuild, Clean Project, 重启 Android Studio，External Libraries 依旧不更新。

### 解决方案

点击 Sync Profile with Gradle File,  第三个按钮就是。

{% img "/images/Sync-Profile-with-Gradle-File.png" %}


