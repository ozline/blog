---
title: '【题解】POJ-3159 Candies 卡Queue+SPFA与Vector'
date: 2022-05-03 21:00:00
tags: 
	- ACM
	- 图论
desc: 
	- FZUACM2020新生训练-图论G
	- POJ3159
categories:
	- POJ题解

---

# POJ-3159 Candies

POJ:http://poj.org/problem?id=3159

## 题目

> During the kindergarten days, flymouse was the monitor of his class. Occasionally the head-teacher brought the kids of flymouse’s class a large bag of candies and had flymouse distribute them. All the kids loved candies very much and often compared the numbers of candies they got with others. A kid A could had the idea that though it might be the case that another kid B was better than him in some aspect and therefore had a reason for deserving more candies than he did, he should never get a certain number of candies fewer than B did no matter how many candies he actually got, otherwise he would feel dissatisfied and go to the head-teacher to complain about flymouse’s biased distribution.
>
> snoopy shared class with flymouse at that time. flymouse always compared the number of his candies with that of snoopy’s. He wanted to make the difference between the numbers as large as possible while keeping every kid satisfied. Now he had just got another bag of candies from the head-teacher, what was the largest difference he could make out of it?

| Time Limit | Memory Limit |
| ---------- | ------------ |
| 1500 ms    | 131072 kB    |

## 思路

这道题实际上就是说，A不允许B比A多C根蜡烛，也就是说`不允许B - A > C`的情况出现，而输入给了多个这样的不等式，实际上是一个差分约束系统

关于差分约束系统，参考：https://oi-wiki.org/graph/diff-constraints/

知道是差分约束后，用Dijkstra或者SPFA什么的都可以了

这道题的思路其实很简单，但是一直TLE，然后查了一下，发现不能用Vector，只要用了Vector就会TLE，其次是如果是SPFA那么不能使用队列优化只能用栈优化，**这道题太阴间了**

已知会TLE的解：

- Bellman-Ford
- 队列优化Bellman-Ford（SPFA）
- 使用了Vector的任何解（可能要划掉任何？）

不过参考了网上题解，这道题倒是让我会了丢掉Vector如何存边

AC的代码使用了**链式前向星**，参考：https://blog.csdn.net/sugarbliss/article/details/86495945

总而言之这就是一道考优化的题嘛

## 代码

### TLE

```c++
/**
 * @author OZLIINEX
 * @brief POJ - 3159 Candies SPFA 卡TLE了..
 * @date 2022-05-03
 */

#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <cmath>
#include <queue>
#include <vector>

#define MAXN 1000001

#define INF 0x3f3f3f3f

typedef long long ll;

using namespace std;

/**
    N is the number of kids in the class
 */
int n, m;

vector<pair<int, int>> Edge[MAXN];
bool vis[MAXN];
int dis[MAXN];

void SPFA()
{
    memset(vis, false, sizeof(vis));
    memset(dis, INF, sizeof(dis));

    queue<int> Q;
    Q.push(1);
    vis[1] = true;
    dis[1] = 0;

    while (!Q.empty())
    {
        int now = Q.front();
        Q.pop();
        vis[now] = false;
        for (int i = 0; i < Edge[now].size(); i++)
        {
            int next = Edge[now][i].first;
            int wishes = Edge[now][i].second;

            if (dis[now] + wishes < dis[next])
            {
                dis[next] = dis[now] + wishes;
                if (vis[next])
                    continue;
                vis[next] = true;
                Q.push(next);
            }
        }
    }
}

int main()
{

    scanf("%d %d", &n, &m);
    int a, b, c;
    while (m--)
    {
        scanf("%d %d %d", &a, &b, &c);
        Edge[a].push_back(make_pair(b, c)); // a期望b不能拿超过c的东西
    }
    SPFA();
    printf("%d\n", dis[n]);
    return 0;
}
```

### AC

```c++
/**
 * @author OZLIINEX
 * @brief POJ - 3159 Candies SPFA
 * @date 2022-05-03
 */

#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <cmath>
#include <queue>
#include <stack>

#define MAXN 200001

#define INF 0x3f3f3f3f

typedef long long ll;

using namespace std;

/**
    N is the number of kids in the class
 */
int n, m,len;


struct Edge
{
    int v, next, w;
}e[MAXN];

int head[MAXN]; //head[from]表示以head为出发点的邻接表表头在数组es中的位置，开始时所有元素初始化为-1
bool vis[MAXN];
int dis[MAXN];

void add(int from,int to,int wish){
    e[len].v = to;
    e[len].next = head[from];
    e[len].w = wish;
    head[from] = len++;
}

void SPFA()
{
    memset(vis, false, sizeof(vis));
    memset(dis, INF, sizeof(dis));

    stack<int> Q;
    Q.push(1);
    vis[1] = true;
    dis[1] = 0;

    while (!Q.empty())
    {
        int now = Q.top();
        Q.pop();
        vis[now] = false;
        for (int i = head[now]; i!=-1; i=e[i].next)
        {
            int v = e[i].v;
            int w = e[i].w;

            if (dis[now] + w < dis[v])
            {
                dis[v] = dis[now] + w;
                if (vis[v])
                    continue;
                vis[v] = true;
                Q.push(v);
            }
        }
    }
}

int main()
{

    scanf("%d %d", &n, &m);
    int a, b, c;
    memset(head,-1,sizeof(head));
    while (m--)
    {
        scanf("%d %d %d", &a, &b, &c);
        add(a,b,c);
    }
    SPFA();
    printf("%d\n", dis[n]);
    return 0;
}
```

## Reference

https://exp-blog.com/algorithm/poj/poj1860-currency-exchange/
http://www.cppblog.com/keroro/archive/2013/05/28/200649.html
https://www.programminghunter.com/article/6472483735/
https://blog.csdn.net/sugarbliss/article/details/86495945
https://oi-wiki.org/graph/diff-constraints/