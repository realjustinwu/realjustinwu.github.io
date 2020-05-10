---
title: "Codeforces Round #640 (Div. 4) - C. K-th Not Divisible by n"
date: 2020-05-10 08:46
tags: [Codeforces, Algorithm]
categories: Algorithm
---

[题目链接](https://codeforces.com/contest/1352/problem/C)

## 题意

求正整数中不能被 n 整除的第 k 个数，如 n=3,k=7。不能被3整除的数是 1,2,4,5,7,8,10,11... 第7个数是 10。

## 题解

从 1 到 $+\infty$，每 n 个分成一组，则每组前 n-1 个是不可以被 n 整除的（好像是废话）。第 k 个不能被 n 整除的数在 (k-1)/(n-1) + 1 组中，位置为 k%(n-1)。很容易找到这个值。

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
#define REPD(i,a,b) for(int i=b;i>a; --i)
#define INF 0x3f3f3f3f
#define ESP 1e-8

using namespace std;

int cas, n, k;

int main()
{
    #ifndef ONLINE_JUDGE
    freopen("c.txt","r",stdin);
    #endif
    SI(cas);
    while(cas--) 
    {
        SI(n); SI(k);
        int t1 = k/(n-1);
        int t2 = k%(n-1);
        int ans = t1 * n + t2; 
        if (t2 == 0) ans -= 1;
        PF("%d\n", ans);
    }
    return 0;
}

```
