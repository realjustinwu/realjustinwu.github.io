---
title: "Codeforces Round #640 (Div. 4) - E. Special Elements"
date: 2020-05-11 20:23
tags: [Codeforces, Algorithm]
categories: Algorithm
---

[题目链接](https://codeforces.com/contest/1352/problem/E)

## 题意

给一个长度为 n 的数字序列，问这些数字中，有多少个数字是可以通过这些数字两个或者多个相连的数字相加得到。

## 题解

<!-- more -->

动态规划，状态是：前面的数与当前的数相加的和，只需要存下和为 1 到 n 的数。

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

#define N 8005

int cas,n,a[N];
bool visit[N];
bool processing[N];

int main()
{
    #ifndef ONLINE_JUDGE
    freopen("e.txt","r",stdin);
    #endif
    SI(cas);
    while (cas--)
    {
        SI(n);
        REP(i, 0, n) SI(a[i]);
        memset(visit, 0,sizeof(visit));
        memset(processing, 0, sizeof(processing));
        REP(i, 1, n)
        {
            REPD(j, n, 0)
            {
                if (j+a[i]>n) processing[j] = false;
                else if (processing[j]) 
                {
                    processing[j] = false;
                    processing[j+a[i]] = true;
                    visit[j+a[i]] = true;
                }
            }
            if (a[i] + a[i-1]<=n)
            {
                visit[a[i] + a[i-1]] = true;
                processing[a[i] + a[i-1]] = true;
            }
        }
        int ans = 0;
        REP(i, 0, n)
        {
            if (visit[a[i]]) ans++;
        }
        PF("%d\n", ans);
    }
    return 0;
}
```
