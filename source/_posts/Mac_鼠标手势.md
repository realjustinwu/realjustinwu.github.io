---
title: Mac 鼠标手势
date: 2018-6-11 20:48:26
categories: 小工具
tags: [mac, apple script, xGestures]
blog: true
---



用习惯了苹果触摸板之后，换成外接键盘和鼠标之后，感觉鼠标真是太难用了。尤其是习惯了三个手指切换工作空间，鼠标根本没法完成这个快捷方式（苹果鼠标可以切换工作空间，但是 ds 我买不起）。

Google 之后发现，除了三个手指之外，还可以用快捷键切换工作空间的，`control + →` 切换到右侧工作空间 `control + ←` 切换到左侧工作空间。之前设置电脑的时候，习惯了三个手指，觉得这个快捷方式没用于是把它禁掉了。在设置中把快捷键勾上后，总算可以不用触摸板就能切换工作空间了。

用了半天之后发现，这种快捷方式不够顺手。如果能把鼠标滚轮的两侧按钮使用上就完美了，摁向左边，切换到左侧工作空间，摁向右边，切换到右侧工作空间。但是没找到我的鼠标的管理软件。然后继续 google，终于找到一个好用的东西 [xGestures][]

[xGestures][] 之前要钱的，现在免费了，感谢作者 Brian Kendall。

使用 xGestures 可以设置各种自定义的鼠标手势，来执行常用操作和非常用操作。其中有一个特别有用的功能，就是可以运行一个 apple script。apple script 是 Mac 上的脚本语言，可以用来执行各种操作。我这里切换工作空间就是使用 apple script 完成的。下面进入正题：

<!-- more -->

## 安装 xGestures

从 http://briankendall.net/xGestures/download.htm 下载 xGestures，下载完成之后，双击安装，安装步骤参考 https://briian.com/6172/

## 自定义手势

1. 修改切换左右工作空间的快捷键

    今天踩了一个小小的坑，发现 `key code 124 using {control down}` 在我的电脑上不起作用，不懂为啥。后面把快捷键改成不包含 ← 和 → 的就好了。所以，要先修改一下快捷键，尽量避免跟其他键冲突

    System Preferences... -> keyboard -> Shortcuts -> Mission Control

    1. 点击 Move left a space -> 回车 -> 同时按下 control + option + command + [
    2. 点击 Move right a space -> 回车 -> 同时按下 control + option + command + ]

2. 自定义 apple script

    在 ~/.mybash 或者其他文件夹中定义文件 gesture_to_next_space.scpt，并输入下面内容

        tell application "System Events"
            key code 33 using {option down, control down, command down}
        end tell

    再新建文件 gesture_to_previous_space.scpt，并输入下面内容

        tell application "System Events"
            key code 33 using {option down, control down, command down}
        end tell



3. 跳转到 xGestures 设置面板
    
    System Preferences... -> xGestures -> Applications 

4. 添加手势
    
    New Gesture -> 选择手势(比如 Up Right) -> Description 可写可不写 -> Gesture action 选择 Run Apple Script -> 点击 Choose file -> 选择刚刚的文件 gesture_to_next_space.scpt

    同理设置 Up Left 手势。最后点击 Apply Settings。

5. 测试是否成功
    
    按住鼠标右键，向上移动鼠标，再向右移动鼠标，松开右键。应该就跳转到右侧工作空间了。

Mac 键盘 KeyCode https://eastmanreference.com/complete-list-of-applescript-key-codes




[xGestures]: http://briankendall.net/xGestures/download.htm