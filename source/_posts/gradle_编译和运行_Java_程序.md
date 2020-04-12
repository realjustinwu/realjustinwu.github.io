---
date: 2015-11-03 23:31:31
title: gradle编译和运行Java程序
categories: Java
tags: [gradle]
blog: true
---



gradle代码

```groovy
apply plugin: 'java'  
apply plugin: 'application'  
  
mainClassName = 'com.binwin20.testjava.Main' // main() 的路径  
```

<!-- more -->

在src/main/java/com/binwin20/testjava中

```java
package com.binwin20.testjava;  
  
public class Main {  
    public static void main(String[] args) {  
        System.out.println("Hello World.");  
    }
}
```

运行java（要提前配置grdle环境）

```bash
gradle -q run
```
