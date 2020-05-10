---
title: "Codeforces Round #640 (Div. 4) - B. Same Parity Summands"
date: 2020-05-10 08:20
tags: [Codeforces, Algorithm]
categories: Algorithm
---

[题目链接](https://codeforces.com/contest/1352/problem/B)

## 题意

能否用 k 个奇数或者 k 个偶数相加得到 n，如果可以，输出一种可行的解。

## 题解

判断 k 和 n 的奇偶性，分4种情况考虑。

1. k>n 时：无解
2. n 是奇数，k 是偶数：无解
3. n 是奇数，k 是奇数：前 k-1 个数都是 1，最后一个 n-k+1
4. n 是偶数，k 是奇数：前 k-1 个数都是 2，最后一个 n-2*k+2，如果最后一个是负数，则无解
5. n 是偶数，k 是偶数：前 k-1 个数都是 1，最后一个 n-k+1

可以化简为：

1. 前 k-1 个数都是 1，最后一个 `n-k+1`，如果 `n-k+1` 是正奇数则有解
2. 前 k-1 个数都是 2，最后一个 `n-2*k+2`，如果 `n-2*k+2` 是正偶数则有解

## 代码

### 1. 化简前

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
    freopen("b.txt","r",stdin);
    #endif
    SI(cas);
    while(cas--)
    {
        SI(n); SI(k);
        if (k>n)
        {
            PF("NO\n");
            continue;
        }
        if (n&1)
        {
            if ((k&1) == 0)
            {
                PF("NO\n");
            }
            else
            {
                PF("YES\n");
                REP(i, 0, k-1) PF("1 ");
                PF("%d\n", n-k+1);
            }
        }
        else
        {
            if (k&1)
            {
                if ((k<<1) > n)
                {
                    PF("NO\n");
                }
                else
                {
                    PF("YES\n");
                    REP(i, 0, k-1) PF("2 ");
                    PF("%d\n", n-(k<<1)+2);
                }
            }
            else
            {
                PF("YES\n");
                REP(i, 0, k-1) PF("1 ");
                PF("%d\n", n-k+1);
            }
        }
    }
    return 0;
}
```

### 2. 化简后

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

int cas, n, k, remaind;

int main()
{
    #ifndef ONLINE_JUDGE
    freopen("b.txt","r",stdin);
    #endif
    SI(cas);
    while(cas--)
    {
        SI(n); SI(k);

        remaind = n-k+1;

        if (remaind>0 && (remaind&1))
        {
            PF("YES\n");
            REP(i, 0, k-1) PF("1 ");
            PF("%d\n", n-k+1);
            continue;
        }
        remaind = n-2*k+2;
        if (remaind>0 && (remaind&1)==0)
        {
            PF("YES\n");
            REP(i, 0, k-1) PF("2 ");
            PF("%d\n", n-(k<<1)+2);
            continue;
        }
        PF("NO\n");
    }
    return 0;
}
```
