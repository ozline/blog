---
title: '【题解】POJ-1860 Currency Exchange'
date: 2022-05-03 17:00:00
tags: 
	- ACM
	- 图论
desc: 
	- FZUACM2020新生训练-图论F
	- POJ1860
categories:
	- POJ题解
---
# POJ-1860 Currency Exchange

POJ:http://poj.org/problem?id=1860

## 题目

> Several currency exchange points are working in our city. Let us suppose that each point specializes in two particular currencies and performs exchange operations only with these currencies. There can be several points specializing in the same pair of currencies. Each point has its own exchange rates, exchange rate of A to B is the quantity of B you get for 1A. Also each exchange point has some commission, the sum you have to pay for your exchange operation. Commission is always collected in source currency.
> For example, if you want to exchange 100 US Dollars into Russian Rubles at the exchange point, where the exchange rate is 29.75, and the commission is 0.39 you will get (100 - 0.39) * 29.75 = 2963.3975RUR.
> You surely know that there are N different currencies you can deal with in our city. Let us assign unique integer number from 1 to N to each currency. Then each exchange point can be described with 6 numbers: integer A and B - numbers of currencies it exchanges, and real RAB, CAB, RBA and CBA - exchange rates and commissions when exchanging A to B and B to A respectively.
> Nick has some money in currency S and wonders if he can somehow, after some exchange operations, increase his capital. Of course, he wants to have his money in currency S in the end. Help him to answer this difficult question. Nick must always have non-negative sum of money while making his operations.
>
> 有很多种货币，货币之间可以兑换，但是需要手续费，而且A->B的汇率、手续费和B->A的汇率、手续费不同，有个人想通过货币交易来赚钱，判断能不能赚钱

| Time Limit | Memory Limit |
| ---------- | ------------ |
| 1000 ms    | 30000 kB     |

## 思路

做这道题的时候学校把这道题归类到图论，然后按前面的做题情况来说，先做了一堆并查集，在上一题又出现了SPFA，这道题就比较容易猜到是SPFA来做，有点投机取巧的味道

然后，这道题首先要注意输入问题，有的并不是int输入，而是double输入（因为这个我卡了半天代码）。

对于一个货币交易点，我们可以知道这个点只能在2种货币间进行兑换，例如一个货币交易点可以兑换A和B的货币，我们所需要抽象的并不是交易点，而应当是各种货币，把货币当成一张图上的各个点，然后各个点之间是一个无向图，但是**往返所需要的花费是不一样的**。通过各个货币建立起来一张图后，我们所需要做的就是在各个货币之间往返，然后查找出能否赚钱

我们利用了SPFA的算法思路（**不断更新一个点一直到这个点不能再被更新**），样本的答案如果会赚钱，那么一定会出现**一个正权环**，在这个环之前不断的走就会一直赚钱（就像其他题解说的能一直松弛），而题目又有一句他最终会回到起点（`he wants to have his money in currency S in the end`），那么我们只需要在起点判断钱会不会增加即可。

因此，在这道题中，dis数组表示的是走到这个点时，我们当前手上所持有的货币金额大小，更新条件就是`(dis[now]-commission)*rate > dis[next]`，也就是经过货币兑换我们会比先前的值更大

## 代码

```c++
/**
 * @author OZLIINEX
 * @brief POJ - 1860 Currency Exchange SPFA
 * @date 2022-05-03
 */

#include <iostream>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <algorithm>
#include <cmath>
#include <vector>
#include <queue>

#define MAXN 100001
#define INF 1 << 30

typedef long long ll;

using namespace std;

/**
    N - the number of currencies
    M - the number of exchange points
    S - the number of currency Nick has
    V - the quantity of currency units he has
 */
int n, m, s;
double v;

// Vector<pair<下一个位置,pair<利率,手续费>>> Edge[当前位置];
vector<pair<int, pair<double, double>>> Edge[MAXN]; //领接表
bool vis[MAXN];
double dis[MAXN]; //到当前这个点时我们所持有的钱

bool SPFA()
{
    memset(vis, false, sizeof(vis));
    memset(dis, 0, sizeof(dis));

    queue<int> Q;
    Q.push(s);
    vis[s] = true;
    dis[s] = v; //设定当前这个位置的钱

    while (!Q.empty())
    {
        int now = Q.front();
        Q.pop();
        vis[now] = false;

        //回到起点，发现钱多了
        if(now == s && dis[now]>v) return true;
        for (int i = 0; i < Edge[now].size(); i++)
        {
            int next = Edge[now][i].first;
            double rate = Edge[now][i].second.first;
            double commission = Edge[now][i].second.second;

            //如果进行货币交易后发现，诶嘿，钱多了
            if ((dis[now] - commission) * rate > dis[next])
            {
                dis[next] = (dis[now] - commission) * rate;
                if(vis[next]) continue;
                vis[next] = true;
                Q.push(next);
            }
        }
    }
    return false;
}

int main()
{
    int a, b;
    double r_ab, c_ab, r_ba, c_ba;
    scanf("%d %d %d %lf", &n, &m, &s, &v);
    while (m--)
    {
        scanf("%d %d %lf %lf %lf %lf", &a, &b, &r_ab, &c_ab, &r_ba, &c_ba);
        Edge[a].push_back(make_pair(b, make_pair(r_ab, c_ab)));
        Edge[b].push_back(make_pair(a, make_pair(r_ba, c_ba)));
    }
    puts(SPFA() ? "YES" : "NO");
    return 0;
}
```

## Reference

https://exp-blog.com/algorithm/poj/poj1860-currency-exchange/