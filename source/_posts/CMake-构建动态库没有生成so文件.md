---
title: CMake 构建动态库没有生成so文件
date: 2017-02-22 20:48:57
tags: [CMake, 踩坑, Sublime]
categories: NDK
blog: true
---



最近开始学 NDK 编程，踩了不少的坑。

在 Android Studio 中生成 so 文件又两种方式，一种使用 ndk-build，一种使用 CMake。

使用 ndk-build 可以在 Android.mk 和 Application.mk 文件中，设置编译属性和依赖等。
每一次编译项目都需要手动执行 `ndk-build` 命令才能生成 so 文件，如果经常修改 C 代码，每次都要重新执行命令，很麻烦。
也可以在 Gradle 中增加 ndk 配置，但是这种方式有缺陷。Android Studio 创建项目时推荐使用第二种方式：使用 CMake。

CMake 是一个跨平台的自动化建构系统，使用 CMakeLists.txt 来描述构建过程。可以产生标准的构建文件，如 Unix 的 Makefile 或Windows Visual C++ 的 projects/workspaces。

于是乎开始了 CMake 的踩坑之旅。找了一本 [CMake 实践 PDF][] 开始学习。

<!-- more -->

在构建动态库时生成不了 so 文件。检查了好几遍，代码和配置跟 PDF 中的一模一样。输出如下：

```bash
MacBook-Pro:build binwin20$ make
Scanning dependencies of target lib
[ 50%] Building C object lib/CMakeFiles/lib.dir/hello.c.o
[100%] Linking C shared library liblib.dylib
[100%] Built target lib
```

看到 liblib.dylib 文件，挺奇怪的，为啥不是 so 文件呢？然后 **在 Sublime 的侧边栏中**找了一下没找到 liblib.dylib 文件，猜想这可能是个中间文件。
网上各种搜就是找不到答案。最后猜想可能是 Mac 编译的不是 so 文件。于是乎启动 Docker 打开 Ubuntu 系统，把项目文件夹直接挂载到 Ubuntu 中，真是方便。下载 CMake 花了不少时间。
执行 make 命令之后，输出如下：

```bash
root@3eb73a8c9dcc:/myDocker/cmake/t3/build# make
Scanning dependencies of target lib
[ 50%] Building C object lib/CMakeFiles/lib.dir/hello.c.o
[100%] Linking C shared library liblib.so
[100%] Built target lib
```

发现 Ubuntu 中有生成 so 文件，**在 Sublime 的侧边栏中** 还是没看到 so 文件，坑啊。又继续找了好久，没找到原因，然后猜想，会不会生成到其他地方了呢？于是执行了一下 find，

```bash
root@3eb73a8c9dcc:/myDocker/cmake/t3/build# find      
.
./cmake_install.cmake
./CMakeCache.txt
...
./lib/CMakeFiles/progress.marks
./lib/liblib.so
./lib/Makefile
./Makefile
```

看到了 liblib.so。这人品，简直要爆了，要不是项目名 lib 字母排在 CMakeFiles 后面，指不定还要找多久才能找到这个文件呢。

然后看看 **Sublime 的侧边栏** 还是没有 so 文件，一脸蒙蔽。难道 Docker 中的文件都还在虚拟机内存中，Mac中看不到？然后在 Mac 中终端里看了一下，有这个文件啊。。。

最后在 Sublime 中 `command + ,`，果然找到了原因。要是早点让我看到 liblib.dylib，也不至于被坑这么久啊。

```json
{
    "folder_exclude_patterns": [".svn", ".git", ".hg", "CVS"],
    "file_exclude_patterns": ["*.pyc", "*.pyo", "*.exe", "*.dll", "*.obj","*.o", "*.a", "*.lib", "*.so", "*.dylib", "*.ncb", "*.sdf", "*.suo", "*.pdb", "*.idb", ".DS_Store", "*.class", "*.psd", "*.db", "*.sublime-workspace"]
}
```

有时间一定要把《程序员的自我修养》看一遍。






[CMake 实践 PDF]: http://sewm.pku.edu.cn/src/paradise/reference/CMake%20Practice.pdf