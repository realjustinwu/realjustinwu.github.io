---
title: "Codeforces Round #640 (Div. 4) - G. Special Permutation"
date: 2020-05-11 20:54
tags: [Codeforces, Algorithm]
categories: Algorithm
---

[题目链接](https://codeforces.com/contest/1352/problem/G)

## 题意

求一个长度为 n 的排列，要求相连两个数字只差是 2到4之间的数。

## 题解

<!-- more -->

1. 从前到后 1, 3, 5, 7, .... 最后面的几个数字先不处理，这样的序列是满足题意的
2. 从后到前 .... 8, 6, 4, 2  最前面的几个数字先不处理，这样的序列是满足题意的
3. 发现，只要最末尾的数字能从奇数位转到偶数位就可以了。
4. 末尾的六个数字 (n-5), (n-2), (n), (n-4), (n-1), (n-3), (n-6)，超过 6 个的数字都有解了
5. 前面 2,3,4,5,6 的特殊处理一下

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

int cas, n;

int main()
{
    #ifndef ONLINE_JUDGE
    freopen("g.txt","r",stdin);
    #endif
    SI(cas);
    while(cas--)
    {
        SI(n);
        if (n==2) PF("-1\n");
        else if (n==3) PF("-1\n");
        else if (n==4) PF("3 1 4 2\n");
        else if (n==5) PF("3 5 2 4 1\n");
        else
        {
            REPD(i, n>>1, 2) PF("%d ", i<<1);
            PF("3 1 4 2 5");
            REP(i, 3, (n+1)>>1)
            {
                if ((i*2+1)<=n) PF(" %d",i*2+1);
            }
            PF("\n");
        }
    }
    return 0;
}
```