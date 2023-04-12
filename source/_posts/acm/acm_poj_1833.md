---
title: '【题解】POJ-1833 排列'
date: 2021-12-22 15:00:00
tags: 
	- ACM
	- 数学
desc: FZUACM2020新生训练-数学C，POJ-1833
categories:
	- POJ题解
---

# POJ-1833 排列


POJ：http://poj.org/problem?id=1833

## 题目

> 题目描述：
> 大家知道，给出正整数n，则1到n这n个数可以构成n！种排列，把这些排列按照从小到大的顺序（字典顺序）列出，如n=3时，列出1 2 3，1 3 2，2 1 3，2 3 1，3 1 2，3 2 1六个排列。
>
> 任务描述：
> 给出某个排列，求出这个排列的下k个排列，如果遇到最后一个排列，则下1排列为第1个排列，即排列1 2 3…n。
> 比如：n = 3，k=2 给出排列2 3 1，则它的下1个排列为3 1 2，下2个排列为3 2 1，因此答案为3 2 1。

## 思路

这是一道字典序算法模板题

找这组数字的下一个排序，算法为：

- 从右至左找到第一个数，这个数满足这个数右边那个数比这个数大，记这个数为a
- 继续从右至左找到第一个比a大的数，记为b
- 交换a和b，再把a后面的数按从小到大重新排序

如果要求下$$ k $$ 个排序，就重复$$ k $$ 次

## 代码

```c++
#include <iostream>
#include <cstdlib>
#include <cstring>
#include <cstdio>
#include <cmath>
#include <algorithm>

#define inFre() freopen("in.txt","r",stdin);
#define outFre() freopen("out.txt","w",stdout);
#define setZero(a) memset(a,0,sizeof(a));
#define MAXMUM 10240

using namespace std;
typedef long long ll;

int main(){
    inFre();
    int m,n,k,p[MAXMUM],tmp1,tmp2;
    scanf("%d",&m);
    for(int m1=0;m1<m;m1++){
        setZero(p);scanf("%d %d",&n,&k);
        for(int i=0;i<n;i++) scanf("%d ",&p[i]);
        while(k>0){
            k--;tmp1=-1;tmp2=-1;
            for(int i=n-1;i>0;i--){
                if(p[i]>p[i-1]){
                    tmp1=i-1;
                    break;
                }
            }
            if(tmp1==-1){
                sort(p,p+n);
            }else{
                for(int i=n-1;i>-1;i--){
                    if(p[i]>p[tmp1]){
                        tmp2=i;
                        break;
                    }
                }
                swap(p[tmp1],p[tmp2]);
                sort(p+tmp1+1,p+n);
            }
        }
        for(int i=0;i<n;i++) printf("%d ",p[i]);
        puts("");
    }
    return 0;
}
```

