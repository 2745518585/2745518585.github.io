---
title: 'P3195 [HNOI2008]玩具装箱'
date: 2022-04-04 09:32:41
tags: [动态规划,单调队列,斜率优化dp]
description: ' '
alias: P3195-solution/index.html
---

[P3195 [HNOI2008]玩具装箱](https://www.luogu.com.cn/problem/P3195)
## 分析
斜率优化 dp 的裸题，用 $s$ 表示前缀和，只是转移方程比较复杂：
$$f[i]=f[j]+(i-j-1+s[i]-s[j]-L)^2$$
我们稍微化简一下，设 $b[i]=s[i]+i$，$c[i]=s[i]+i+L+1$，上述转移方程就可以写成：
$$f[i]=f[j]+(b[i]-c[j])^2$$
$$f[i]=f[j]+b[i]^2-2 \times b[i] \times c[j]+c[j]^2$$
$$f[j]+c[j]^2=2 \times b[i] \times c[j]+f[i]-b[i]^2$$
于是，我们可以得到 $Y=f[j]+c[j]^2$，$A=2 \times b[i]$，$X=c[j]$，$B=f[i]-b[i]^2$
## code
```cpp
#include<cstdio>
#include<algorithm>
using namespace std;
typedef long long ll;
const int N=1000001;
int n,m,a[N],Q[N*2],T=0,R=0;
ll b[N],c[N],f[N];
ll abc(int x,int y)
{
    if(c[x]==c[y]) return 1e18;
    return ((f[x]+c[x]*c[x])-(f[y]+c[y]*c[y]))/(c[x]-c[y]);
}
int main()
{
    scanf("%d%d",&n,&m);
    c[0]=m+1;
    for(int i=1;i<=n;++i)
    {
        scanf("%d",&a[i]);
        b[i]=b[i-1]+a[i]+1;
        c[i]=c[i-1]+a[i]+1;
    }
    for(int i=1;i<=n;++i)
    {
        while(T<R&&abc(Q[T],Q[T+1])<2*b[i]) ++T;
        f[i]=f[Q[T]]+b[i]*b[i]-2*b[i]*c[Q[T]]+c[Q[T]]*c[Q[T]];
        while(T<R&&abc(Q[R],i)<abc(Q[R-1],Q[R])) --R;
        Q[++R]=i;
    }
    printf("%lld",f[n]);
    return 0;
}
```