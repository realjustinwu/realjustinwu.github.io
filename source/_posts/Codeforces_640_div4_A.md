---
title: "Codeforces Round #640 (Div. 4) - A. Sum of Round Numbers"
date: 2020-05-10 08:09
tags: [Codeforces, Algorithm]
categories: Algorithm
---

[题目链接](https://codeforces.com/contest/1352/problem/A)

## 题意

有一种 "round" 类型的数据，格式是 d000，给一个数字n，计算 n 是由多少个 "round" 数字相加得到的

<!-- more -->

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

int n,a,b,ans,i;

int main()
{
    #ifndef ONLINE_JUDGE
    freopen("a.txt","r",stdin);
    #endif
    SI(n);
    while(n--)
    {
        SI(a);
        b = a;
        ans = 0;
        i = 0;
        while(b>0) {
            if (b % 10 != 0) ans += 1;
            b /= 10;
        }
        PF("%d\n", ans);
        while(a>0) {
            if (a%10 != 0)
            {
                PF("%d", a%10);
                REP(j, 0, i) PF("0");
                PF(" ");
            }
            i+=1;
            a /= 10;
        }
        PF("\n");
    }
    return 0;
}
```
