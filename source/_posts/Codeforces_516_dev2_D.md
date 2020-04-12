---
title: "Codeforces #516 dev2 D: Labyrinth"
date: 2018-10-25 21:23:03
categories: Algorithm
tags: [Algorithm, Codeforces]
blog: true
---

#### 题目链接

http://codeforces.com/contest/1064/problem/D

#### 题意

你在玩一个走一个迷宫，可以向上、向下走无限次，只能向左走 x 次，向右走 y 次，* 号的点是不能走的。问：能够到达的格子数是多少？

格子大小是 n 行 m 列，起始位置是 c,r (从 1 开始)

#### 解法

<!-- more -->

一般的 BFS。

开始的时候，把起点所在的列全部加入队列并标记为已经访问。

从队列中取出一个点，如果能向左走，并且左边的列没有被标记，把这一列都加入队列并标记已经访问，向左走的次数减一，同理处理右边。

计算访问过的点。

#### 代码

```cpp
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <queue>
#include <deque>

using namespace std;

#define N 2009

char map[N][N];
bool visit[N][N];
int n,m;
int x,y;
int l,r;

struct Node
{
    int x,y,l,r;
    Node(int x, int y, int l, int r): x(x), y(y), l(l), r(r) {}
};

deque<Node> que;

void pushColum(int curX, int curY, int curL, int curR) 
{
    que.push_back(Node(curX, curY, curL, curR));
    visit[curX][curY] = true;
    for (int i=curX-1; i>=0; i--)
    {
        if (map[i][curY] == '*' || visit[i][curY])
            break;
        que.push_back(Node(i, curY, curL, curR));
        visit[i][curY] = true;
    }
    for (int i=curX+1; i<n; i++)
    {
        if (map[i][curY] == '*' || visit[i][curY])
            break;
        que.push_back(Node(i, curY, curL, curR));
        visit[i][curY] = true;
    }
}

int main(int argc, char const *argv[])
{
    //freopen("D1.txt", "r", stdin);
    memset(visit, false, sizeof(visit));
    scanf("%d %d", &n, &m);
    scanf("%d %d", &x, &y);
    scanf("%d %d", &l, &r);
    for (int i=0; i<n; i++)
    {
        scanf("%s", map[i]);
    }
    x--;y--;
    pushColum(x, y, l, r);
    visit[x][y] = true;

    int curX,curY,curL,curR;
    while (!que.empty())
    {
        Node node = que.front();
        que.pop_front();
        curX = node.x;
        curY = node.y;
        curL = node.l;
        curR = node.r;
        int checkY = curY-1;
        if (checkY>=0 && map[curX][checkY] != '*' && curL > 0 && !visit[curX][checkY])
        {
            pushColum(curX, checkY, curL - 1, curR);
        }
        checkY = curY+1;
        if (checkY<m && map[curX][checkY] != '*' && curR > 0 && !visit[curX][checkY])
        {
            pushColum(curX, checkY, curL, curR - 1);
        }
    }
    int ans = 0;
    for (int i=0; i<n; i++)
    {
        for (int j=0; j<m; j++)
        {
            if (visit[i][j])
                ans ++;
        }
    }
    printf("%d\n", ans);
    return 0;
}
```

开始做的时候，只把起始点加入队列，取出一个点之后，再把这个点所在的列都加到队列，再考虑点的左右两边。这样是有问题的，下面的 test case 过不了， 想了整整一晚上。😭😭😭

```
8 13
1 1
100 10
..........#..
..#######.#..
#..#####..#..
##..####.####
###..###.....
####..###....
#####..##....
######.......
```




