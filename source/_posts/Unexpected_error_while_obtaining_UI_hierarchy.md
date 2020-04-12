---
title: Unexpected error while obtaining UI hierarchy
date: 2018-04-28 13:58:37
categories: Android
tags: [uiautomatorview, Android, sdkmanager]
blog: true
---



使用 uiautomatorview 截屏的时候遇到错误，Unexpected error while obtaining UI hierarchy。

原因是 SDK 的 tools 没有更新到最新，Android Studio 更新到 3.0 之后，就不能直接从 Android Studio 中打开 SDK Manager 了。
Android studio 自带的 Android SDK 不包括对 uiautomatorviewer-26.0.0-dev.jar 的更新，所以这个文件一直是 uiautomatorviewer-25.xx.jar，不支持 Android 8.1 的系统是理所当然的。

<!-- more -->

使用命令行运行 `sdkmanager` 会提示 sdkmanager 的使用方式。然后更新一下 SDK `sdkmanager --update`，再运行 uiautomatorview，就能正常工作了。

更新之后，再次运行 `sdkmanager` 发现不提示使用方式了。

```
$ sdkmanager
[=======================================] 100% Computing updates...
```

记录一下 sdkmanager 更新前的使用方式：

```
$ sdkmanager
Usage: 
  sdkmanager [--uninstall] [<common args>] [--package_file <file>] [<packages>...]
  sdkmanager --update [<common args>]
  sdkmanager --list [<common args>]
  sdkmanager --licenses [<common args>]

In its first form, installs, or uninstalls, or updates packages.
    By default, the listed packages are installed or (if already installed)
    updated to the latest version.

    --uninstall: uninstalled listed packages.

    <package> is a sdk-style path (e.g. "build-tools;23.0.0" or
             "platforms;android-23").
    <package-file> is a text file where each line is a sdk-style path
                   of a package to install or uninstall.
    Multiple --package_file arguments may be specified in combination
    with explicit paths.

In its second form (with --update), all installed packages are
    updated to the latest version.

In its third form, all installed and available packages are printed
    out.

In its fourth form (with --licenses), show and offer the option to
     accept licenses for all available packages that have not already been
     accepted.

Common Arguments:
    --sdk_root=<sdkRootPath>: Use the specified SDK root instead of the SDK 
                              containing this tool

    --channel=<channelId>: Include packages in channels up to <channelId>.
                           Common channels are:
                           0 (Stable), 1 (Beta), 2 (Dev), and 3 (Canary).

    --include_obsolete: With --list, show obsolete packages in the
                        package listing. With --update, update obsolete
                        packages as well as non-obsolete.

    --no_https: Force all connections to use http rather than https.

    --proxy=<http | socks>: Connect via a proxy of the given type.

    --proxy_host=<IP or DNS address>: IP or DNS address of the proxy to use.

    --proxy_port=<port #>: Proxy port to connect to.

* If the env var REPO_OS_OVERRIDE is set to "windows",
  "macosx", or "linux", packages will be downloaded for that OS.

```
