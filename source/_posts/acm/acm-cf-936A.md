---
title: '【题解】CodeForces-936A SAVE ENERGY!'
date: 2021-12-13 02:30:00
tags: 
	- ACM
	- 数学
desc: FZUACM2020新生训练-数学A，CodeForces-936A
categories:
	- CodeForces题解

---

# CodeForces-936A SAVE ENERGY!

CF：https://codeforces.com/problemset/problem/936/A

## 题目

> Julia is going to cook a chicken in the kitchen of her dormitory. To save energy, the stove in the kitchen automatically turns off after *k* minutes after turning on.
>
> During cooking, Julia goes to the kitchen every *d* minutes and turns on the stove if it is turned off. While the cooker is turned off, it stays warm. The stove switches on and off instantly.
>
> It is known that the chicken needs *t* minutes to be cooked on the stove, if it is turned on, and 2*t* minutes, if it is turned off. You need to find out, how much time will Julia have to cook the chicken, if it is considered that the chicken is cooked evenly, with constant speed when the stove is turned on and at a constant speed when it is turned off.

> Julia要烤一个鸡，但是她的厨房为了省电，炉子会在开k分钟后自动关掉，Julia会间隔d分钟检查炉子有没有在工作，如果在没在工作就把炉子开起来，已知整个炉子一直开着的话要t分钟烤熟，没开着的话可以靠余温2t分钟烤熟，问总共要多少分钟能烤熟

## 思路

这是一道数学题，我们需要知道一个周期，这个周期指的是炉子从开始到关闭再到被Julia检查到的时间，把烤鸡这个过程微分，计算一个周期中，这个鸡可以烤到什么程度，然后累加，最后对最后一个周期（通常是不完整的）进行处理。

周期公式：$$ T=((k-1)/d+1)*d $$

T为long long 型，这个公式表示求k和d的最小覆盖周期

例如：

$ 当k=3,d=5则T=5 $

$ 当k=5,d=3则T=6 $

$ 当k=4,d=2则T=4 $

当然，即使不知道这个公式，也可以通过一些基础算法求出这个最小覆盖周期。

在这个最小覆盖周期中，炉子从开到关，期间可能存在一段炉子关着但是没有被检查到的时间，根据题意，这段时间炉子烤鸡的速度是正常速度的一半，因此我们可以定义一个周期中烤鸡实际上相当于烤了多久，也就是cycle

接着，我们对最后一个周期（这个周期通常是不完整的）进行一下处理，首先是算出这个最后一个周期中需要烤多久

也就是$ tmp=t-ll(t/cycle)*cycle $，其中$ ll(t/cycle) $利用了向下取整表示出完整循环的周期数量

如果$ tmp<k $，也就意味着在最后一个周期这个炉子一直都是开着，那我们就直接加上这段时间就好了

如果$ tmp>=k $，则最后一个周期中存在炉子关掉的情况，这时候我们需要2倍的时间来烤

## 代码

```c++
#include <iostream>

using namespace std;
typedef long long ll;

int main()
{
    ll k, d, t, T;
    double ans, cycle, tmp;
    scanf("%lld %lld %lld", &k, &d, &t);
    T = ((k - 1) / d + 1) * d;
    cycle = (T - k) * 0.5 + k;
    tmp = t - ll(t / cycle) * cycle;
    ans = ll(t / cycle) * T;
    ans += tmp < k ? tmp : k + (tmp - k) * 2;
    printf("%.1lf\n", ans);
    return 0;
}
```

