---
title: "Codeforces #516 dev2 E: Dwarves, Hats and Extrasensory Abilities"
date: 2018-10-25 22:04:41
categories: Algorithm
tags: [Algorithm, Codeforces]
blog: true
---

#### 题目链接

http://codeforces.com/contest/1064/problem/E

#### 题意

这个题跟玩游戏一样，首先给你一个数字 n，表示有多少个人，你要给每一个人一个位置，然后再给出一条直线。系统会给每一个人戴白色或者黑色帽子，你要保证在直线同侧的人的帽子颜色相同。

游戏是这样玩的：
1. 系统告诉你有 n 个人
2. 你输出第一个人的位置 x1, y1
3. 系统给你一个输入 black/white 表示第一个人的帽子颜色
4. 你输出第二个人的位置 x1, y1
5. 系统给你一个输入 black/white 表示第二个人的帽子颜色
6. ....
7. 系统给你地 n 个人的帽子颜色之后，你要输出 x1 y1 x2 y2 表示给出的直线
8. 系统会检测你给的直线是否满足要求

#### 解法

<!-- more -->

把所有的人放在同一条直线上 (y==10)，然后把相同颜色的人归类到同一边。
我们先给位置，系统在告诉我们颜色，所以我们都是把人放在中央，系统给了颜色之后，再确定它属于左边还是右边。

1. 我们先给出第一个点 (0, 10), 系统给我们颜色之后(设为 black)，我们就可以定下 0 的位置是 black，10^9 位置是 white
2. 然后把中点告诉给系统 ((0+10^9)/2, 10)，等系统给了颜色之后，我们再归类。
3. 最后的直线，我们从两个就很简单了，随便画一条斜线就可以了。

#### 代码

```cpp
#include <cstdio>
#include <iostream>

using namespace std;

int n;
char color[10], firstColor;

int main(int argc, char const *argv[])
{
    //freopen("E.txt", "r", stdin);
    cin>>n;
    cout<<0<<" "<<10<<endl;
    cin>>color;
    firstColor = color[0];
    int l = 0, r = 1000000000, mid;
    while (--n)
    {
        mid = (l+r) >> 1;
        cout<<mid<<" "<<10<<endl;
        cin>>color;
        if (color[0] == firstColor)
        {
            l = mid;
        } 
        else
        {
            r = mid;
        }
    }
    cout<<l<<" "<<20<<" "<<r<<" "<<0<<endl;
    return 0;
}
```

其实就是一个二分，还是被坑了，需要使用 cout>> 方式输出。

