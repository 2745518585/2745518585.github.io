---
title: 'P3628 [APIO2010]特别行动队'
date: 2022-04-04 09:32:51
tags: [动态规划,单调队列,斜率优化dp]
description: ' '
---

[P3628 [APIO2010]特别行动队](https://www.luogu.com.cn/problem/P3628)
## 分析
这时一道斜率优化 dp 的裸题，简单地推一下转移方程，$s$ 为战斗力前缀和：
$$f[i]=f[j]+a \times (s[i]-s[j])^2+b \times (s[i]-s[j])+c$$
$$f[i]=f[j]+a \times s[i]^2-2 \times a \times s[i] \times s[j]+a \times s[j]^2+b \times s[i]-b \times s[j]+c$$
$$f[j]-a \times s[j]^2+b \times b[j]=2 \times a \times s[i] \times s[j]+f[i]-a \times s[i]^2-b \times s[i]-c$$
可以得到 $Y=f[j]-a \times s[j]^2+b \times b[j]$，$A=2 \times a \times s[i]$，$X=s[j]$，$B=f[i]-a \times s[i]^2-b \times s[i]-c$
## code
```cpp
#include<cstdio>
#include<algorithm>
using namespace std;
typedef long long ll;
const int N=3000001;
int n,a,b,c,Q[N*2],T=0,R=0;
ll d[N],f[N];
ll abc(int x,int y)
{
    if(d[x]==d[y]) return 1e18;
    return ((f[x]+a*d[x]*d[x]-b*d[x])-(f[y]+a*d[y]*d[y]-b*d[y]))/(d[x]-d[y]);
}
int main()
{
    scanf("%d%d%d%d",&n,&a,&b,&c);
    for(int i=1;i<=n;++i)
    {
        int x;
        scanf("%d",&x);
        d[i]=d[i-1]+x;
    }
    for(int i=1;i<=n;++i)
    {
        while(T<R&&abc(Q[T],Q[T+1])>2*a*d[i]) ++T;
        f[i]=f[Q[T]]+a*(d[i]-d[Q[T]])*(d[i]-d[Q[T]])+b*(d[i]-d[Q[T]])+c;
        while(T<R&&abc(Q[R-1],Q[R])<abc(Q[R],i)) --R;
        Q[++R]=i;
    }
    printf("%lld",f[n]);
    return 0;
}
```