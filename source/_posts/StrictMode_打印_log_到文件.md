---
title: StrictMode 打印 log 到文件
date: 2019-1-22 16:03:41
categories: Android
tags: [Android, Strict Mode]
blog: true
---



Strict Mode 是 Android 性能调优的工具，用来检查程序运行时有哪些违例的情况。比如在主线程进行耗时操作、检查 Activity 是否泄露、检查 Closeable 对象是否未关闭(如，Sqlite Cursor 对象)。

如何使用及其原理可以查看[技术小黑屋](https://droidyue.com/blog/2015/09/26/android-tuning-tool-strictmode/)。

配置完成之后，发现 log 只能打印到 logcat 中，不能自定义输出到指定的文件。

<!-- more -->

尝试了两种方案，都不行

1. 把 logcat 输出到指定文件，[Stack Overflow](https://stackoverflow.com/questions/19565685/saving-logcat-to-a-text-file-in-android-device)，这里的权限 READ_LOGS 只能给 system app。
2. 使用 [android-hidden-api](https://github.com/anggrayudi/android-hidden-api) 替换 SDK 中的 android.jar 文件，直接访问 @hide 类和方法。替换 api-28 编译不通过，替换 api-27 打包出来之后，安装到手机(Android P 系统)，会提示找不到 @hide 的类 ViolationLogger。

最后只能使用 StrictMode 类中提供的新方法 penaltyListener()，但是这种方式只能在 Android-P 上使用。

```java
if (BuildConfig.DEBUG) {
    StrictMode.ThreadPolicy.Builder builder1 = new StrictMode.ThreadPolicy.Builder();
    builder1.detectAll().penaltyLog();
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.P) {
        builder1.penaltyListener(Executors.newSingleThreadExecutor(),
                v -> Log.d(TAG, "StrictMode()[Thread]: " + v));
    }
    StrictMode.setThreadPolicy(builder1.build());
    StrictMode.VmPolicy.Builder builder2 = new StrictMode.VmPolicy.Builder();
    builder2.detectAll().penaltyLog();
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.P) {
        builder2.penaltyListener(Executors.newSingleThreadExecutor(),
                v -> Log.d(TAG, "StrictMode()[Vm]: " + v));
    }
    StrictMode.setVmPolicy(builder2.build());
}
```
