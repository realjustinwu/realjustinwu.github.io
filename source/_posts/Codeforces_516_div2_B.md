---
title: "Codeforces 516 Div2 B: Equations of Mathematical Magic"
date: 2018-10-25 20:20:07
categories: Algorithm
tags: [Algorithm, Codeforces]
blog: true
---



#### 题目链接

http://codeforces.com/contest/1064/problem/B

#### 题意

给你一个数 a，从 0 到 a 有多少个数满足等式 `a-(a^x)-x = 0`？其中 "^" 是异或操作符。

#### 解法

<!-- more -->

可以化简为 `a-x=a^x`，然后一位一位考虑

| a | x | a-x | a^x |
| - | - | - | - |
| 1 | 0 | 1 | 1 |
| 1 | 1 | 0 | 0 |
| 0 | 0 | 0 | 0 |
| 0 | 1 | 1(需要退位) | 1 |

- 可以看出 a 的某一位上是 1 的时候，x 的这个位上是 0 或者 1 都能使等式成立。
- 当 a 的某一位上是 0 的时候，x 的这个位上是 0 时，能时等式成立。

下面考虑最后一种需要退位的情况：
1. 如果上一位是表中的前 3 种情况，那么原来相等的，就会变成不相等。
2. 如果上一位也是 `0 1` 这种情况，就会变成 `1 1`，上一位也满足了，需要向上上位退位。
3. a 的前面的位必然会出现 1 的情况，因为 a>=x。
4. 所以 0 1 是不满足的。

总结就是 a 的某一位上是 0，x 在对应的位上只能是 0，如果 a 的某一位上是 1，x 在对应的位上可以是 0 或者 1。

#### 代码

```cpp
#include <cmath>
#include <cstdlib>
#include <cstdio>

int main(int argc, char const *argv[])
{
    //freopen("B.txt", "r", stdin);
    int n,a;
    scanf("%d", &n);
    while (n--) 
    {
        scanf("%d", &a);
        int k = 0;
        while (a>0)
        {
            if (a&1) k++;
            a >>= 1;
        }
        printf("%d\n", (1<<k));
    }
    return 0;
}
```


