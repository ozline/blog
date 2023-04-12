---
title: '【题解】POJ-1797 Heavy Transportation 三种解法'
date: 2022-05-02 23:30:00
tags: 
	- ACM
	- 图论
desc: FZUACM2020新生训练-图论E,POJ1797
categories:
	- POJ题解

---
# POJ-1797 Heavy Transportation

POJ:http://poj.org/problem?id=1797

## 题目

> Background
> Hugo Heavy is happy. After the breakdown of the Cargolifter project he can now expand business. But he needs a clever man who tells him whether there really is a way from the place his customer has build his giant steel crane to the place where it is needed on which all streets can carry the weight.
> Fortunately he already has a plan of the city with all streets and bridges and all the allowed weights.Unfortunately he has no idea how to find the the maximum weight capacity in order to tell his customer how heavy the crane may become. But you surely know.
>
> Problem
> You are given the plan of the city, described by the streets (with weight limits) between the crossings, which are numbered from 1 to n. Your task is to find the maximum weight that can be transported from crossing 1 (Hugo's place) to crossing n (the customer's place). You may assume that there is at least one path. All streets can be travelled in both directions.

| Time Limit | Memory Limit |
| ---------- | ------------ |
| 3000ms     | 30000kB      |

这道题参考了多个题解和教程，尝试着带着参考写了4种不同的答案（其中Dijkstra有2个），实际上这道题是入门图论题，只是我自己水平还有很大提升空间

**这道题实际上求的是最大生成树，所以可以看到在一些地方会与标准的最小生成树符号反了**

## 方法一：Kruskal

### 思路

对边进行由大到小的排序，然后逐个边合并，一直到连通节点1（起始点）和节点n（终点）结束（代码里通过判断两个根节点是否一致来得出是否连通），**由于我们边是按从大到小排序的，所以答案就是连通节点的最后一条边能支持的最大重量**。嗯..模板无疑

### 代码

```c++
/**
 * @author OZLIINEX
 * @brief POJ - 1797 Heavy Transportation Kruskal
 * @date 2022-05-02
 */

#include <iostream>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <algorithm>
#include <cmath>

#define MAXN 100001

typedef long long ll;

using namespace std;

struct Edge
{
    ll start;
    ll end;
    ll heavy;
};

ll plans;        //计划数
ll n, m;         //十字路口的条数（点的数量） 路径数量
ll ans;          //答案
ll father[MAXN]; //根节点
Edge heavy[MAXN];

bool compare(Edge m, Edge n)
{
    return m.heavy > n.heavy;
}

void set_make(ll n)
{
    for (int i = 1; i <= n; i++)
        father[i] = i;
}

ll set_find(ll x)
{
    if (x != father[x])
        father[x] = set_find(father[x]);
    return father[x];
}

void set_union(ll x, ll y)
{
    ll fx = set_find(x);
    ll fy = set_find(y);

    if (fx != fy)
        father[fy] = fx;
}

int main()
{

    memset(heavy, 0, sizeof(heavy)); //重置为0
    scanf("%d", &plans);
    for (int current = 0; current < plans; current++)
    {
        scanf("%lld %lld", &n, &m);
        for (int i = 0; i < m; i++)
            scanf("%lld %lld %lld", &heavy[i].start, &heavy[i].end, &heavy[i].heavy);

        set_make(MAXN-1);
        sort(heavy, heavy + m, compare);
        ll k = 1;
        for (int i = 0; i < m; i++)
        {
            ans = i;
            if (k == n)
                break;
            if (set_find(heavy[i].start) != set_find(heavy[i].end))
            {
                set_union(heavy[i].start, heavy[i].end);
                k++;
                if (set_find(1) == set_find(n))
                    break;
            }
        }

        printf("Scenario #%d:\n%d\n\n", current + 1, heavy[ans].heavy); //输出答案
    }
    return 0;
}
```



## 方法二： 带队列优化的Bellman-Ford （SPFA）

### 思路

B站学习了一圈，再参考了其他人的题解，实际上这个SPFA法解题只需要改一个地方：

标准的spfa的dis数组记录的是距离（最小距离balabala），但因为题目原因，这道题需要把dis数组改为从起始点到当前点的最大承重量

因此只需要把标准的`dis[v]>dis[now]+Edge[now][i].second`改为`min(dis[now], Edge[now][i].second) > dis[Edge[now][i].first]`就好了，当然，下面那个更新状态的语句也要改。改完就能跑通了（跑通就过！

### 代码

```c++
/**
 * @author OZLIINEX
 * @brief POJ - 1797 Heavy Transportation SPFA
 * @date 2022-04-29
 */

#include <iostream>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <algorithm>
#include <cmath>
#include <queue>
#include <vector>

#define MAXN 1000001
#define INF 1 << 30

typedef long long ll;

using namespace std;

int plans; //计划数
int n, m;  //十字路口的条数（点的数量） 路径数量

vector<pair<int, int>> Edge[MAXN];
bool vis[MAXN];
int dis[MAXN];

void SPFA()
{
    memset(vis, false, sizeof(vis)); //重置为0
    memset(dis, 0, sizeof(dis));

    queue<int> Q;
    Q.push(1);
    vis[1] = true;
    dis[1] = INF;

    while (!Q.empty())
    {
        int now = Q.front();
        Q.pop();
        for (int i = 0; i < Edge[now].size(); i++)
        {
            int next = Edge[now][i].first;
            int heavy = Edge[now][i].second;
            if (min(dis[now], heavy) > dis[next])
            {
                dis[next] = min(dis[now], heavy);
                if (!vis[next])
                {
                    vis[next] = true;
                    Q.push(next);
                }
            }
        }
        vis[now] = 0;
    }
}

int main()
{
    scanf("%d", &plans);
    for (int current = 0; current < plans; current++)
    {
        for (int i = 0; i < MAXN; i++)
            Edge[i].clear();
        int tmp_start, tmp_end, tmp_heavy;
        scanf("%d %d", &n, &m);
        for (int i = 0; i < m; i++)
        {
            scanf("%d %d %d", &tmp_start, &tmp_end, &tmp_heavy);
            Edge[tmp_start].push_back(make_pair(tmp_end, tmp_heavy));
            Edge[tmp_end].push_back(make_pair(tmp_start, tmp_heavy));
        }
        SPFA();
        printf("Scenario #%d:\n%d\n\n", current + 1, dis[n]); //输出答案
    }
    return 0;
}
```



## 方法三：Dijkstra

### 思路

太模板了，其实一些思路与方法一的思路还是很像的
**更新条件是：**`1.这个点没有被访问过 2.这个点到起始点的代价比前面确定的点到起始点的代价和当前点到前面确定点的代价都要小`

### 代码

#### 带优先队列优化的

```c++
/**
 * @author OZLIINEX
 * @brief POJ - 1797 Heavy Transportation Dijkstra
 * @date 2022-05-02
 */

#include <iostream>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <algorithm>
#include <cmath>
#include <queue>
#include <vector>

#define MAXN 10001
#define INF 1e7

typedef long long ll;

using namespace std;

int plans; //计划数
int n, m;  //十字路口的条数（点的数量） 路径数量

vector<pair<int, int>> Edge[MAXN];
int dis[MAXN];

void Djistra()
{
    memset(dis, 0, sizeof(dis));

    priority_queue<pair<int, int>> Q; //第二个是我们要表示的点，而第一个仅仅只是用于排序而已
    dis[1] = INF;
    Q.push(make_pair(dis[1], 1));

    while (!Q.empty())
    {
        int now = Q.top().second;
        Q.pop();
        for (int i = 0; i < Edge[now].size(); i++)
        {
            int next = Edge[now][i].first;
            int heavy = Edge[now][i].second;
            if (min(dis[now], heavy) > dis[next])
            {
                dis[next] = min(dis[now], heavy);
                Q.push(make_pair(dis[next], next));
            }
        }
    }
}

int main()
{
    scanf("%d", &plans);
    for (int current = 0; current < plans; current++)
    {
        for (int i = 0; i < MAXN; i++)
            Edge[i].clear();
        int tmp_start, tmp_end, tmp_heavy;
        scanf("%d %d", &n, &m);
        for (int i = 0; i < m; i++)
        {
            scanf("%d %d %d", &tmp_start, &tmp_end, &tmp_heavy);
            Edge[tmp_start].push_back(make_pair(tmp_end, tmp_heavy));
            Edge[tmp_end].push_back(make_pair(tmp_start, tmp_heavy));
        }
        Djistra();
        printf("Scenario #%d:\n%d\n\n", current + 1, dis[n]); //输出答案
    }
    return 0;
}
```

#### 不带优先队列优化的

```c++
/**
 * @author OZLIINEX
 * @brief POJ - 1797 Heavy Transportation Dijkstra
 * @date 2022-05-02
 */

#include <iostream>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <algorithm>
#include <cmath>

#define MAXN 2001

typedef long long ll;

using namespace std;

int plans;           //计划数
int n, m;            //十字路口的条数（点的数量） 路径数量
int vis[MAXN * 100]; //是否访问
int d[MAXN * 100];   //当前点到起点的最大承载重量
int heavy[MAXN][MAXN];

void djistra(int start, int end)
{
    memset(vis, 0, sizeof(vis));
    for (int i = 1; i <= n; i++)
        d[i] = heavy[i][start];
    vis[start] = 1; //第一个点已经被访问啦
    for (int i = 1; i < m; i++)
    {
        int point = 0;
        int maxHeavy = 0;

        //找一条承载重量最大且没被访问的边
        for (int j = 1; j <= n; j++)
        {
            if (!vis[j] && d[j] > maxHeavy)
                maxHeavy = d[j], point = j;
        }
        if (!maxHeavy) //没有找到合适的边
            break;
        vis[point] = 1; //标记这个点为已访问
        //对于每一条边，进行松弛
        for (int j = 1; j <= n; j++)
        {
            //1.这个点没有被访问过 2.这个点到起始点的代价比前面确定的点到起始点的代价和当前点到前面确定点的代价都要小
            if (!vis[j] && d[j] < min(d[point], heavy[point][j])){
                d[j] = min(d[point], heavy[point][j]); //更新这个点的最大承载力的最大值
            }
        }
    }
}

int main()
{
    scanf("%d", &plans);
    for (int current = 0; current < plans; current++)
    {
        memset(heavy, 0, sizeof(heavy)); //重置为0
        memset(d, 0, sizeof(d));         //重置为0
        int tmp_start, tmp_end, tmp_heavy;
        scanf("%d %d", &n, &m);
        for (int i = 0; i < m; i++)
        {
            scanf("%d %d %d", &tmp_start, &tmp_end, &tmp_heavy);
            heavy[tmp_start][tmp_end] = heavy[tmp_end][tmp_start] = tmp_heavy;
        }
        djistra(1, n);
        printf("Scenario #%d:\n%d\n\n", current + 1, d[n]); //输出答案
    }
    return 0;
}
```

## Reference

https://zh.wikipedia.org/zh-cn/%E5%85%8B%E9%B2%81%E6%96%AF%E5%85%8B%E5%B0%94%E6%BC%94%E7%AE%97%E6%B3%95
https://zh.m.wikipedia.org/zh-sg/%E6%9C%80%E7%9F%AD%E8%B7%AF%E5%BE%84%E5%BF%AB%E9%80%9F%E7%AE%97%E6%B3%95
https://zh.m.wikipedia.org/wiki/%E5%85%8B%E9%B2%81%E6%96%AF%E5%85%8B%E5%B0%94%E6%BC%94%E7%AE%97%E6%B3%95
https://www.programminghunter.com/article/5506934098/
https://www.cnblogs.com/zxy160/p/7215156.html
https://jqh.zone/blog/post_detail/515/post_detail/377
https://blog.csdn.net/u011265346/article/details/42914843

