---
title: "Codeforces Round #640 (Div. 4) - D. Alice, Bob and Candies"
date: 2020-05-11 20:16
tags: [Codeforces, Algorithm]
categories: Algorithm
---

[题目链接](https://codeforces.com/contest/1352/problem/D)

## 题意

给了一个数字序列，长度是 n，Alice 和 Bob 分别从左右两端取数字，可以取一个或多个，要求每次取的数字严格大于另一个人上一次取的数字，当大于的时候停止取数字，让另一个人取。问要多少轮取完所有数字，两个人分别取了多少数字。

## 题解

模拟

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
#define PF printf
#define REP(i,a,b) for(int i=a; i<b; ++i)
#define REPD(i,a,b) for(int i=a; i>b; --i)
#define INF 0x3f3f3f3f
#define ESP 1e-8

using namespace std;

#define N 1005

int cas, n, a[N];
int aCount, bCount, aCurrent, bCurrent, index1, index2;

int main()
{
    #ifndef ONLINE_JUDGE
    freopen("d.txt","r",stdin);
    #endif
    SI(cas);
    while(cas--)
    {
        SI(n);
        REP(i, 0, n) SI(a[i]);
        aCount = 0; 
        bCount = 0;
        aCurrent = 0;
        bCurrent = 0;
        index1 = 0;
        index2 = n-1;
        int step =0;
        while(index1 <= index2)
        {
            step++;
            aCurrent = 0;
            bool finish = false;
            REP(i, index1, index2+1)
            {
                aCurrent += a[i];
                aCount += a[i];
                if (aCurrent > bCurrent)
                {
                    index1 = i + 1;
                    finish = true;
                    break;
                }
            }
            if (index1>index2 || !finish) break;
            step++;
            bCurrent = 0;
            finish = false;
            REPD(i, index2, index1-1)
            {
                bCurrent += a[i];
                bCount += a[i];
                if (bCurrent > aCurrent)
                {
                    index2 = i - 1;
                    finish = true;
                    break;
                }
            }
            if (index1>index2 || !finish) break;
        }
        PF("%d %d %d\n", step, aCount, bCount);
    }
    return 0;
}
```
