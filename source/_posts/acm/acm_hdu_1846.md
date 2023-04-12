---
title: '【题解】HDU-1846 Brave Game'
date: 2022-05-14 23:30:00
tags: 
	- ACM
	- 博弈论
categories:
	- HDU题解



---

# HDU-1846 Brave Game

HDU：https://acm.hdu.edu.cn/showproblem.php?pid=1846

## 题目

> 十年前读大学的时候，中国每年都要从国外引进一些电影大片，其中有一部电影就叫《勇敢者的游戏》（英文名称：Zathura），一直到现在，我依然对于电影中的部分电脑特技印象深刻。
> 今天，大家选择上机考试，就是一种勇敢（brave）的选择；这个短学期，我们讲的是博弈（game）专题；所以，大家现在玩的也是“勇敢者的游戏”，这也是我命名这个题目的原因。
> 当然，除了“勇敢”，我还希望看到“诚信”，无论考试成绩如何，希望看到的都是一个真实的结果，我也相信大家一定能做到的~
>
> 各位勇敢者要玩的第一个游戏是什么呢？很简单，它是这样定义的：
> 1、 本游戏是一个二人游戏;
> 2、 有一堆石子一共有n个；
> 3、 两人轮流进行;
> 4、 每走一步可以取走1…m个石子；
> 5、 最先取光石子的一方为胜；
>
> 如果游戏的双方使用的都是最优策略，请输出哪个人能赢。

## 思路

巴什博奕例题，结论如下

$$ n\%(m+1)==0 $$

其中n是这一堆石子的个数，m是最多可以取的石子数，**当满足上述公式时，先手必败**

## 分析

这一部分摘自文章结尾Ref里ACMBook关于巴什博弈的分析，我觉得写的非常棒啊！！

我们称先进行游戏的人为先手，另一个人为后手。

1.若

$$ n=m+1 $$

由于一次最多只能拿m个，因此不论先手怎么拿（甚至拿1个），后手永远都能全部拿走剩下的内容，后者必赢

2.若

$$ n=(m+1)*r+s $$

其中`r为任意自然数，s<=m`先手拿走s个物品，若后手拿走`k(k<=m)`个，那么先手再拿走`m+1-k`个，结果又剩下了`(m+1)*(r-1)`个，回到最初的起点！因此如果出现上述公式情形，先手是必赢的。

因此，**只要给对手留下(m+1)的倍数，那么我们必定会赢**

最近好累啊。。

## 代码

```C++
/**
 * @author OZLIINEX
 * @brief HDU - 1846 Brave Game Bash博弈
 * @date 2022-05-07
 */

#include <iostream>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <cmath>
#include <algorithm>
#include <vector>
#include <queue>

#define MAXN 200001

typedef long long ll;

using namespace std;

int c;
int m, n;

int main()
{
    scanf("%d", &c);
    while (c-- && ~scanf("%d %d", &n, &m))
        puts(n % (m + 1) == 0 ? "second" : "first");
    return 0;
}
```

## References

https://hrbust-acm-team.gitbooks.io/acm-book/content/game_theory/ba_shi_bo_yi.html
