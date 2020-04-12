---
title: 树莓派3 安装 Raspbian 镜像
date: 2018-05-06 19:36:33
categories: 树莓派
tags: [树莓派]
blog: true
---



1. 下载 Raspbian 镜像

    到[官网](https://www.raspberrypi.org/downloads/) 选择适合的镜像并下载，下载之后解压得到 img 文件

2. 刻录到 SD 卡
    
    下载 SD 卡写入工具 [Etcher](https://etcher.io/)，运行之后，选择刚刚下载的 img 文件，然后选择需要刻的目标 SD 卡

3. 设置 ssh 登录

    在 SD 卡根目录创建空文件 ssh，参考 https://howchoo.com/g/ote0ywmzywj/how-to-enable-ssh-on-raspbian-without-a-screen

        $ cd /Volumes/boot
        $ touch ssh

<!-- more -->

4. 设置 wifi 密码 https://howchoo.com/g/ndy1zte2yjn/how-to-set-up-wifi-on-your-raspberry-pi-without-ethernet

    在 SD 卡根目录创建文件 wpa_supplicant.conf

        $ cd /Volumes/boot
        $ vim wpa_supplicant.conf
        ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
        network={
            ssid="YOUR_NETWORK_NAME"
            psk="YOUR_PASSWORD"
            key_mgmt=WPA-PSK
        }

5. 启动 Raspberry

    将 SD 卡装回到树莓派中，连接显示器和键盘，接通电源

