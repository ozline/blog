---
title: '【题解】CodeForces-321A Ciel and Robot'
date: 2021-12-22 02:00:00
tags: 
	- ACM
	- 数学
desc: FZUACM2020新生训练-数学B，CodeForces-321A
categories:
	- CodeForces题解

---

# CodeForces-321A Ciel and Robot

CF：https://codeforces.com/problemset/problem/321/A

## 题目

> Fox Ciel has a robot on a 2D plane. Initially it is located in (0, 0). Fox Ciel code a command to it. The command was represented by string *s*. Each character of *s* is one move operation. There are four move operations at all:
>
> - 'U': go up, (x, y) →  (x, y+1);
> - 'D': go down, (x, y) →  (x, y-1);
> - 'L': go left, (x, y) →  (x-1, y);
> - 'R': go right, (x, y) →  (x+1, y).
>
> The robot will do the operations in *s* from left to right, and repeat it infinite times. Help Fox Ciel to determine if after some steps the robot will located in (*a*, *b*).

> 从$$ (0,0) $$ 开始在二维平面上行走，给定无限循环，问能否抵达某个点

## 思路

简单次数的模拟可以得出具有一定的周期性，即：

假设走完一次循环抵达$$ (M,N) $$ 点，也就是前进M，向上N（这里M,N不一定是正数），若可以抵达目标点，那么必然存在一个点，这个点满足：

- 这个点经过$ k $次的循环(一次循环前进M，向上N)就刚好抵达目标点
- 这个点在行走过程中会经过

例如样例中，目标点是$$ (2,2) $$ ，循环是$$ UR $$ ，那么：

对于$$ (1,1) $$ 这个点而言，进行$$ 1 $$ 次循环，就可以抵达目标点，也就是我们前面分析的条件

## 坑

首先，我们要考虑到，**这个k次循环，这句话已经赋予这个k必须大于0了**，我们不能做一个数值为负的循环，不符合数学逻辑

我们在写代码中，习惯性的把这个求k的过程写为：$$ 距离差/目标点 $$，但是我们是否考虑过，这个目标点可能刚好在轴上呢？也就是$$ M可能等于0，N也可能等于0 $$，因此，我们需要处理这个情况

我第一次写代码的时候，处理这个情况我使用了枚举法，判断m和n然后一系列操作

但是我们能不能想一下：

### 当m,n均为0

意味着这个机器人走一个循环，那么我们只需要判断1次循环内能否到这个点，而**不可能会再经过k次循环**，因此这个k完全可以设为1

### 当m,n有一个为0

意味着这个机器人走一个循环最终会回到某一个轴上，但这时候对于不为0的数m（或n）是不是就存在循环呢？而对于为0的数n（或m），就算乘上k次循环，最终不也都为0吗？因此我们只需要考虑不为0的那一边

### 当m,n均不为0

意味着这个机器人会停在某一象限里，这时候似乎会产生两个k？

事实上，这两个k必须相等，因为你横坐标和纵坐标经历的循环的次数是一致的

## 失误

- 这道题不能用$$ while(scanf()!=EOF) $$，原因未知，只知道使用上去测试点1都过不了，可能跟某些换行符有关
- 输出不是全大写，因为这个点我调了将近四个小时的代码

## 代码

```c++
#include <iostream>
#include <map>
#include <cstring>
#include <cstdio>

#define N 101
#define inFre() freopen("in.txt", "r", stdin);
#define outFre() freopen("out.txt", "r", stdout);
#define setZero(a) memset(a, 0, sizeof(a));

using namespace std;
typedef long long ll;

int main()
{
    inFre() int a, b, len = 0;
    int x[N], y[N];
    char c[N];
    bool ans = false;
    scanf("%d %d", &a, &b);
    scanf("%s", c);
    setZero(x);
    setZero(y);
    for (int i = 0; i < strlen(c); i++)
    {
        switch (c[i])
        {
        case 'U':
            x[i + 1] = x[i], y[i + 1] = y[i] + 1;
            break;
        case 'D':
            x[i + 1] = x[i], y[i + 1] = y[i] - 1;
            break;
        case 'L':
            x[i + 1] = x[i] - 1, y[i + 1] = y[i];
            break;
        case 'R':
            x[i + 1] = x[i] + 1, y[i + 1] = y[i];
            break;
        }
    }
    len = strlen(c);
    if (a == 0 && b == 0)
    { //特判
        puts("Yes\n");
        return 0;
    }

    for (int i = 1; i <= len; i++)
    { //从1开始判断
        int tmpA = a - x[i];
        int tmpB = b - y[i];
        int tmp = (x[len] != 0 ? tmpA / x[len] : (y[len] != 0 ? tmpB / y[len] : 1));
        if (x[len] * tmp == tmpA && y[len] * tmp == tmpB && tmp >= 0)
        {
            ans = true;
            break;
        }
    }
    puts(ans ? "Yes" : "No");
    return 0;
}
```

