---
title: "Codeforces Round #640 (Div. 4) - F. Binary String Reconstruction"
date: 2020-05-11 20:47
tags: [Codeforces, Algorithm]
categories: Algorithm
---

[题目链接](https://codeforces.com/contest/1352/problem/F)

## 题意

一个二进制数，把相连两个数字取出，如 “1110011110” 取出后 "11", "11", "10", "00", "01", "11", "11", "11", "10"。
其中 00 有 n0 个，11 有 n2 个，01 或者 10 有 n1 个。现在给你 n0,n1,n2 返回去求一个满足条件的二进制数。

## 题解

<!-- more -->

1. 先处理 n1, 即 01010101... 有 n1+1 个数字
2. 在其中一个 0 的位置后插入 n0 个0
3. 在其中一个 1 的位置后插入 n2 个1

## 代码

```cpp
#include <cstdio>
#include <cmath>
#include <cstring>
#include <iostream>
#include <queue>
#include <map>
#include <stack>
#include <string>
#include <vector>
#include <list>
#include <deque>

#define LL long long
#define DB double
#define SI(a) scanf("%d", &a)
#define SD(a) scanf("%lf", &a)
#define SS(a) scanf("%s", a)
#define MM(a,b) memset(a,b,sizeof(a))
#define PF printf
#define REP(i,a,b) for(int i=a; i<b; ++i)
#define REPD(i,a,b) for(int i=a; i>b; --i)
#define INF 0x3f3f3f3f
#define ESP 1e-8

using namespace std;

int cas,n0,n1,n2;

int main()
{
    #ifndef ONLINE_JUDGE
    freopen("f.txt","r",stdin);
    #endif
    SI(cas);
    while(cas--)
    {
        SI(n0); SI(n1); SI(n2);
        if (n1 == 0)
        {
            REP(i, 0, n0+n2+1)
            {
                n2>0 ? PF("1"): PF("0");
            }
            PF("\n");
            continue;
        }
        REP(i, 0, n0+1)
        {
            PF("0");
        }
        REP(i, 0, n2+1)
        {
            PF("1");
        }
        REP(i, 0, n1-1)
        {
            (i&1) == 0? PF("0"): PF("1");
        }
        PF("\n");
    }
    return 0;
}
```
