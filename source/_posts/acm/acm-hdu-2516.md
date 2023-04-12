---
title: '【题解】HDU-2516 取石子游戏'
date: 2022-05-05 20:30:00
tags: 
	- ACM
	- 博弈论
desc: FZUACM2020新生训练-博弈论C，HDU-2516
categories:
	- HDU题解


---

# HDU-2516 取石子游戏

HDU：https://acm.hdu.edu.cn/showproblem.php?pid=2516

## 题目

> 1堆石子有n个,两人轮流取.先取者第1次可以取任意多个，但不能全部取完.以后每次取的石子数不能超过上次取子数的2倍。取完者胜.先取者负输出"Second win".先取者胜输出"First win".

## 思路

这是关于**斐波那契博弈**的题，结论是：

> 若N为Fibonacci数，则先手必胜

可以直接用结论做题，没有代码含量

## 证明

首先需要有一个数论基础

> 任何正整数都可以表示为若干个不连续的Fibonacci数之和
>
> Fibonacci性质：
>
> 1. $$ f(n) = f(n-2)+f(n-1) $$
> 2. $$ 2f(n)>f(n+1) $$
> 3. $$ 4f(n)<3f(n+1) $$

### 若N为Fibonacci数

当`n=1`时，`f(n)`=2，无论怎么拿，先拿的必定输

当`n=2`时，`f(n)`=3，无论怎么拿，先拿的必定输

现在假设当`n=k`时满足结论（结论在前面），则当`n=k+1`时，我们可以由性质得出

$$ f(k+1)=f(k-1)+f(k)<f(k-1)+2f(k-1) = 3f(k-1) $$

也就是

$$ f(k+1)<3f(k-1) $$

这时候，如果先手直接拿走`f(k-1)`个，那么这一堆剩下的就没比`2f(k-1)`个少，后手可以直接拿走，因此先手的如果要赢，必须拿少于`f(k-1)`个，而因为这个假设已经是`n=k`的情况了，那么`n=k-1`是已经被证明满足结论的了，因此，如果先拿`f(k-1)`个的话，先手必定输，这个推导过程同样适用于`f(k)`的情况。

### 若N不为Fibonacci数

由先前的数论基础我们可以知道，任何正整数均可以表示为若干个不连续的Fibonacci数之和。也就是说，对于这样的n，我们可以把它改成：

$$ n=f(a_n)+f(a_{n-1})+...+f(a_1) $$

假设先手拿走了`f(a1)`这一堆。由于Fibonacci数不连续，则

$$ f(a_2)>f(a_1)+1 $$ 

也就是说

$$ f(a_2)>2f(a_1) $$

这里假设`a1=1,a2=3`就可以验证这个结论的正确性（因为不是连续的数，而且我们选取的数是整个Fibonacci数列中最相近的数）熟悉吗？后手不可能直接把`f(a2)`堆拿完，一定会有剩的，那么这时候又轮到我们先手了，这时候我们就可以把剩下的全部拿走了

### 结论

**若N为Fibonacci数，则先手必胜**

## 代码

```C++
/**
 * @author OZLIINEX
 * @brief HDU - 2516 取石子游戏 Fibonacci博弈
 * @date 2022-05-05
 */

#include <iostream>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <cmath>
#include <algorithm>
#include <vector>
#include <queue>

#define MAXN 100001

typedef long long ll;

using namespace std;

int n;
ll Fibonacci[MAXN] = {0};
bool ans;

void init()
{
    Fibonacci[0] = 1;
    Fibonacci[1] = 1;
    for (int i = 2; i < MAXN; i++)
        Fibonacci[i] = Fibonacci[i - 1] + Fibonacci[i - 2];
}

int main()
{
    init();
    while (~scanf("%d", &n) && n)
    {
        ans = false;
        for (int i = 0; i < MAXN; i++)
            if (Fibonacci[i] == n)
            {
                ans = true;
                break;
            }

        puts(ans ? "Second win" : "First win");
    }
    return 0;
}
```

## References

https://blog.csdn.net/ACdreamers/article/details/8586135
https://blog.csdn.net/l1832876815/article/details/81136041
https://www.cnblogs.com/jiu0821/p/4638165.html