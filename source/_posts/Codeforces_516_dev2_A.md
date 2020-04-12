---
title: "Codeforces 516 dev2 A: Make a triangle!"
date: 2018-10-22 20:22:50
categories: Algorithm
tags: [Codeforces, Algorithm]
blog: true
---



#### 题目链接

http://codeforces.com/contest/1064/problem/A

#### 题意

给你三根棍子，长度分别是 a,b,c，使用这三跟棍子拼成三角形。如果没法拼成的话，可以加长其中的边，问：把边加长多少能拼出三角形。

#### 解法

<!-- more -->

任意两边之和大于大三边就能拼成三角形。

#### 代码

```cpp
#include <cstdio>
#include <algorithm>

using namespace std;

int main(int argc, char const *argv[])
{
    //freopen("A.txt", "r", stdin);
    int a,b,c, out = -1;
    scanf("%d %d %d", &a, &b, &c);
    out = max(out, a - b - c);
    out = max(out, b - a - c);
    out = max(out, c - a - b);
    printf("%d\n", max(out + 1, 0));
    return 0;
}
```

好长时间没写 C++ 代码了，做两题练练手。
