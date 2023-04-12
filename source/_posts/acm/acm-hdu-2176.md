---
title: '【题解】HDU-2176 取(m堆)石子游戏'
date: 2022-05-05 23:30:00
tags: 
	- ACM
	- 博弈论
categories:
	- HDU题解

---

# HDU-2176 HDU-1850 取(m堆)石子游戏

【HDU-2176】HDU：https://acm.hdu.edu.cn/showproblem.php?pid=2176
【HDU-1850】HDU：https://acm.hdu.edu.cn/showproblem.php?pid=1850

## 题目（HDU-2176）

> m堆石子,两人轮流取.只能在1堆中取.取完者胜.先取者负输出No.先取者胜输出Yes,然后输出怎样取子.例如5堆 5,7,8,9,10先取者胜,先取者第1次取时可以从有8个的那一堆取走7个剩下1个,也可以从有9个的中那一堆取走9个剩下0个,也可以从有10个的中那一堆取走7个剩下3个.

## 思路

首先是，HDU-2176和HDU-1850这两题本质是一样的（包括代码）

Nim游戏**先手必败**条件：

$$ a_1 \oplus a_2 \oplus a_3 \oplus a_4 \oplus ... \oplus a_n = 0 $$

具体过程在Ref里写的很棒啦

而不论是HDU-2176还是HDU-1850，都要求如果先手必胜，那么输出怎样取石子（或者说是输出获胜的情况）

注意，这时候我们已经拿到ANS的值了（而且必定不为0），我们需要做的是，改变某一堆的数量`ai`为`ai'`，使得先手必胜变为先手必败（注意，之所以要让先手必胜局面变成先手必败局面，因为第二次是对方取石子，要让对方处于必败局面才可以），也就是说，改变这堆石头后，再进行异或运算，得出的ANS是0

而我们知道，异或同一个数字，答案是0

$$ ans \oplus ans = 0 $$

因此我们只需要作出如下更改：

$$ ai'=ai \oplus ans $$

代入原公式，答案就是0了，同时我们保证`ai'<ai`，因为我们是拿走石头

这样，代码就不难了

## 代码

### HDU-2176

```C++
/**
 * @author OZLIINEX
 * @brief HDU - 2176  取(m堆)石子游戏 Nim游戏
 * @date 2022-05-06
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

int m;
int list[MAXN]; // list[MAXN] 表示石头堆
int ans;

int main()
{
    while (~scanf("%d", &m) && m)
    {
        ans = 0;
        int tmp;
        for (int i = 0; i < m; i++)
        {
            scanf("%d", &list[i]);
            ans ^= list[i];
        }

        if (!ans)
        {
            puts("No");
            continue;
        }

        puts("Yes");

        for (int i = 0; i < m; i++)
        {
            tmp = ans ^ list[i];
            if (list[i] > tmp)
                printf("%d %d\n", list[i], tmp);
        }
    }
}
```

### HDU-1850

```c++
/**
 * @author OZLIINEX
 * @brief HDU - 1850 Being a Good Boy in Spring Festival Nim游戏
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

int m;
int list[MAXN]; // list[MAXN] 表示石头堆
int ans;
int ansNum;

int main()
{
    while (~scanf("%d", &m) && m)
    {
        ans = 0;
        int tmp;
        for (int i = 0; i < m; i++)
        {
            scanf("%d", &list[i]);
            ans ^= list[i];
        }

        if (!ans)
        {
            puts("0");
            continue;
        }

        ansNum=0;

        for (int i = 0; i < m; i++)
        {
            tmp = ans ^ list[i];
            if (list[i] > tmp)
                ansNum++;
        }

        printf("%d\n",ansNum);
    }
}
```



## References

https://zh.wikipedia.org/zh-sg/%E5%B0%BC%E5%A7%86%E6%B8%B8%E6%88%8F
https://blog.csdn.net/qq_45458915/article/details/102693126
