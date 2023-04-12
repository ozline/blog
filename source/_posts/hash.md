---
title: Hash函数
date: 2021-10-17 01:35:43
tags: ACM
---

# Hash

通过一个函数H，可以把字符串映射成一个数字

满足$x!=y$时，$H(x)==H(y)$的概率很小

# 应用

- 假设有n个长度为L的字符串，问其中最多有几个字符串是相等的

- 直接比较两个长度是L的字符串是否相等的时间复杂度是$O(L)$的
- 因此需要枚举$O(n²)$对字符串进行比较，时间复杂度是$O(n²)$
- 如果把每个字符串都用一个哈希函数映射成一个整数
- 问题就转变为查找一个序列的众数
- 时间复杂度就变成了$O(L)$



其实可以类比成压缩吧..

# 字符串哈希

一个设计良好的字符串哈希函数可以让我们用$O(L)$的时间复杂度预处理，之后每次获取这个字符串的一个子串的哈希值都只需要$O(1)$的时间

# BKDR Hash

基本思想：把一个字符串当做k进制数来处理

```c++
int k=19,M=1e9+7;
int BKDRHash(char *str){
    int ans=0;
    for(int i=0;str[i];i++)
        ans=(1LL*ans*k+str[i])%M;
    	//1LL:把int类型转变成long long进行运算
    return ans;
}
```

### 使用

假设字符串s的下标从1开始，长度为n

```c++
ha[0]=0;
for(int i=1;i<=n;i++)
    ha[i]=(ha[i-1]*k+str[i])%M
```

所以可以知道$ha[i]$就是$s[1..i]$的BKDRHash

现在询问$s[x..y]$的BKDRHash

通过公式可以发现

$ha[y]=s[1]*k^{y-1}+s[2]*k^{y-2}+...+s[x-1]*k^{y-x+1}+s[x]*k^{y-x}+...+s[y]$

注意到$ha[x-1]=s[1]*k^{x-2}+s[2]*k^{x-3}+...+s[x-1]$

而我们要求的$s[x...y]$的哈希值为$s[x]*k^{y-x}+...+s[y]$

可以发现$s[x..y]=ha[y]-ha[x-1]*k^{y-x+1}$

因此预处理出ha数组和k的幂次，每次询问$s[x..y]$的哈希值，只要$O(1)$的时间

# EQ

### 问题描述

鲍勃向你提出了Q个问题：在每个问题中，他给定你一个整数x，请你告诉他有多少个位置，满足“字符串A从该位置开始的后缀子串”与B匹配的长度恰好为x。

$1 ≤ N,M,Q ≤ 200000$

例如：

例如：A=aabcde，B=ab，则A有aabcde、abcde、bcde、cde、de、e这6个后缀子串，它们与B=ab的匹配长度分别是1、2、0、0、0、0。因此A有4个位置与B的匹配长度恰好为0，有1个位置的匹配长度恰好为1，有1个位置的匹配长度恰好为2。

### 分析

理解题意，核心问题是：给定两个字符串A,B，求出A的每个后缀子串和B的最长公共前缀，标准做法是扩展KMP，时间复杂度呈线性



前面已经提到，我们可以用$O(n)$预处理O(1)处理出一个子串的哈希值

求字符串$A[i..n]$与字符串$B[1..m]$的最长公共前缀？

可以用二分

二分长度mid

计算$A[i..i+mid-1]$和$B[1..mid]$的哈希值并进行比较

时间复杂度是$O(log\ n)$

### 代码

```c++
ll getha(int x, int y){
	return ha[y] - ha[x - 1] * p[y - x + 1]; 
} 
ll gethb(int x, int y){
	return hb[y] - hb[x - 1] * p[y - x + 1]; 
}

int main(){ 
	scanf("%d%d%d", &n, &m, &Q); 
	scanf("%s", a+1); 
	scanf("%s", b+1); 
	p[0]=1; 
	for(int i=1;i<=max(n,m);i++) p[i]=p[i-1]*P;
	for(int i=1;i<=n;i ++)	ha[i]=ha[i-1]*P+a[i];
	for(int i=1;i<=m;i ++) 	hb[i]=hb[i-1]*P+b[i]; 
    for(int i = 1;i <= n;i ++){
	int l=1,r=min(m,n-i+1),mid;
	while(l<=r){
		mid=(l+r)>>1;//位运算
		if(getha(i,i+mid-1) == gethb(1,mid)){
			l=mid+1;
		}else{ 
			r=mid-1; 
		}
 	}
	cnt[r]++; 
}

```

