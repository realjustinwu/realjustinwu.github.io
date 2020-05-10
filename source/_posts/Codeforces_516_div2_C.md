---
title: "Codeforces #516 div2 C: Oh Those Palindromes"
date: 2018-10-25 21:09:01
categories: Algorithm
tags: [Algorithm, Codeforces]
blog: true
---



#### 题目链接

http://codeforces.com/contest/1064/problem/C

#### 题意

给你一串字符串，让你排序，使最长回文子串最多。回文子串就是，字符串中截取一小段，这个小段正读倒回来读，都是相同的字符串。

#### 解法

<!-- more -->

凭感觉，相同的字符放一起，能构成更多的回文串。看数据量，给的是 100000，快排时间不够，基数排序可以解决。

#### 代码

```cpp
#include <cstdio>

int main(int argc, char const *argv[])
{
    //freopen("C.txt", "r", stdin);
    int n;
    char a[100009];
    int count[256];
    scanf("%d", &n);
    scanf("%s", a);
    for (int i=0; i<n; i++) 
    {
        count[a[i]] ++;
    }
    for(int i=0; i<256; i++)
    {
        while (count[i] > 0) 
        {
            printf("%c", i);
            --count[i];
        }
    }
    printf("\n");
    return 0;
}
```

