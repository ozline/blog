---
title: '【题解】HDU-2089 不要62'
date: 2021-12-26 11:30:00
tags: ACM
desc: FZUACM2020新生训练-动态规划A，HDU-2089
categories:
	- HDU题解

---

# HDU-2089 不要62


HDU：https://acm.dingbacode.com/showproblem.php?pid=2089

## 题目

> 杭州人称那些傻乎乎粘嗒嗒的人为62（音：laoer）。
> 杭州交通管理局经常会扩充一些的士车牌照，新近出来一个好消息，以后上牌照，不再含有不吉利的数字了，这样一来，就可以消除个别的士司机和乘客的心理障碍，更安全地服务大众。
> 不吉利的数字为所有含有4或62的号码。例如：
> 62315 73418 88914
> 都属于不吉利号码。但是，61152虽然含有6和2，但不是62连号，所以不属于不吉利数字之列。
> 你的任务是，对于每次给出的一个牌照区间号，推断出交管局今次又要实际上给多少辆新的士车上牌照了。

> 输入的都是整数对n、m（0<n≤m<1000000），如果遇到都是0的整数对，则输入结束。
>
> 对于每个整数对，输出一个不含有不吉利数字的统计个数，该数值占一行位置。

## 思路

数位dp，搜索记忆

这是我学习动态规划的第一道题，这道题可能难度从广义上讲很简单，但我确实花了一些时间来学习

参考了这个博主写的题解：https://www.cnblogs.com/2000liuwei/p/10604834.html

这位博主从题设条件仅为`不要4的牌照`开始说起，上手难度低，而且便于理解，他提供了3份代码，一份是没有dp纯粹dfs的代码，另一份是有dp的dfs的代码，而这两份代码都只解决了`不要4的牌照`这个问题，最后才贴上了STD，而STD仅仅只是在第二份代码上添了几行罢了

但这道题的思路是可以学习的，对于输入数字后首先储存每位数字（num数组），然后开始搜索，搜索过程中会判断是否触发limit（上限，也就是说搜索抵达了边界条件，也就是最大数字），若没有触发，则进行全搜索，搜索过程中储存答案，便于下次搜索时直接调用，减少时间复杂度

更多详细的内容可以直接参考我这份并没有作什么修改的代码，我在里面添加了注释：

## 代码

```c++
#include <cstdio>
#include <cstdlib>
#include <algorithm>
#include <cstring>
#include <iostream>

#define infreopen() freopen("in.txt","r",stdin);
#define outfreopen() freopen("out.txt","w",stdout);
#define setzero(a) memset(a,0,sizeof(a));

using namespace std;
typedef long long ll;

ll num[101];
ll dp[101][2];
//这里的dp又叫记忆化搜索,保留搜索结果，减少时间复杂度
//这个dp储存了，数字长度在len下，没有6和有6时的结有效牌照数量
//而当触发limit=时，这个dp不储存（因为不完整），这时候就用到了普通的dfs搜索

//这里面的dfs从高位搜索到低位
ll dfs(int len,bool ifsix,bool limit){
    if(!len) return 1;
    if(!limit&&dp[len][ifsix]!=-1) return dp[len][ifsix];
    ll ans=0;
    ll maxn=limit?num[len]:9;
    for(int i=0;i<=maxn;i++){
        if(i==4) continue;//遇到4直接跳过
        if(i==2&&ifsix) continue;//遇到62跳过
        ans+=dfs(len-1,i==6,limit&&i==maxn);
    }
    if(!limit) dp[len][ifsix]=ans;
    return ans;
    //这个limit指的是触发边界条件（指的就是m和n）时的一些操作，比如不计入dp里面（因为这时候统计不完整）
  	//当然，这个limit只有每位都是上限的时候才会触发，这也是为什么会有limit&&i==maxn这个语句
  	//例如我们搜索101，必须当百位是1（limit=true）且十位刚好等于0的时候，个位数的dfs才会触发limit
}

ll solve(ll x){
    setzero(num);
    int len=0;
    while(x){
        num[++len]=x%10;
        x/=10;
    }
    //把数字拆成每一位储存到num数组里，利用len表示长度
    return dfs(len,false,true);
}

int main(){
    infreopen();
    ll m,n;
    memset(dp,-1,sizeof(dp));
    //把dp初始化放到外面，这样遇到多个数据都可以调用同一个dp，快速减少时间复杂度
    while(~scanf("%lld %lld",&m,&n)){
        if(m==0&&n==0) break;
        printf("%lld\n",solve(n)-solve(m-1));
    }
    return 0;
}
```

