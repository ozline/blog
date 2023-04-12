---
title: '【题解】HDU-1013 Digital Roots'
date: 2021-12-23 23:00:00
tags: ACM
desc: FZUACM2020新生训练-数学D，HDU-1013
categories:
	- HDU题解

---

# HDU-1013 Digital Roots

HDU：https://acm.dingbacode.com/showproblem.php?pid=1013

## 题目

> The digital root of a positive integer is found by summing the digits of the integer. If the resulting value is a single digit then that digit is the digital root. If the resulting value contains two or more digits, those digits are summed and the process is repeated. This is continued as long as necessary to obtain a single digit.
>
> For example, consider the positive integer 24. Adding the 2 and the 4 yields a value of 6. Since 6 is a single digit, 6 is the digital root of 24. Now consider the positive integer 39. Adding the 3 and the 9 yields 12. Since 12 is not a single digit, the process must be repeated. Adding the 1 and the 2 yeilds 3, a single digit and also the digital root of 39.

## 思路

没有思路，按题目走就是对的

关键在于：要认识到c++对于int类型的除法，不是四舍五入而是取整，整个过程就没什么大问题了

## 代码

```c++
#include <cstdio>
#include <cstdlib>
#include <cstring>

#define inFre() freopen("in.txt","r",stdin);
#define outFre() freopen("out.txt","w",stdout);
#define setZero(a) memset(a,0,sizeof(a));
#define MAXMUM 1000001

using namespace std;
typedef long long ll;

int main(){
    inFre();
    char c[MAXMUM];
    int len=0,sum=0,tmp=0;
    while(~scanf("%s",c)){
        if(c[0]==48) break;
        len=strlen(c);
        for(int i=0;i<len;i++) sum+=c[i]-48;
        while(sum>9){
            tmp=sum;sum=0;
            while(tmp){
                sum+=tmp%10;tmp/=10;
            }
        }
        printf("%d\n",sum);sum=0;
    }
    return 0;
}
```

