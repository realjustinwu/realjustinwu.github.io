---
title: 树莓派3 安装 AndroidThings
date: 2018-05-06 11:14:31
categories: AndroidThings
tags: [树莓派, AndroidThings]
blog: true
---



树莓派 3 安装 Android Things, 参考 https://developer.android.com/things/hardware/raspberrypi

1. 下载安装工具

    打开 https://partner.android.com/things/console/u/0/#/tools 在侧边栏中点击 Tools，然后点击 Download 下载安装工具
    下载完成之后解压，可以看到 4 个文件，分别是

    - android-things-setup-utility-linux
    - android-things-setup-utility-macos
    - android-things-setup-utility-windows.exe
    - README.md

<!-- more -->

2. image 写入到 sd 卡

    跳转到解压目录，运行 `sudo android-things-setup-utility-macos`, 然后依次选择，

    - What do you want to do?: 1 - Install Android Things and optionally set up Wi-Fi
    - What hardware are you using?: 1 - Raspberry Pi 3
    - Do you want to use the default image or a custom image?: 1 - Default image:
    
    全部下载完成之后，插入 sd 卡，然后按回车。等待镜像写入到 sd 卡，需要挺长一段时间。

3. 连接网络

    将 SD 卡插入到树莓派，连接鼠标、屏幕和电源。然后连接网络（图形界面操作）

4. Android studio 连接 AndroidThings

    通过 wifi 连接 AndroidThings 设备

        adb connect Android.local:5555
        # 成功之后提示 connected to Android.local:5555

    Android Studio 写完代码之后，点 Run 按钮，可以选择 AndroidThings 设备




