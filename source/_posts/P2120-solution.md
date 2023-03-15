---
title: 'P2120 [ZJOI2007]仓库建设'
date: 2022-04-04 09:32:30
tags: [动态规划,单调队列,斜率优化dp]
description: ' '
---

[P2120 [ZJOI2007]仓库建设](https://www.luogu.com.cn/problem/P2120)
## 分析
这道题是一道很经典又很裸的斜率优化 dp，用 $f[i]$ 表示在 $i$ 号点建立仓库的最小费用，用斜率优化即可。但是转移的时候，我们需要知道第 $j+1$ 个仓库至第 $i$ 个仓库之间的所有仓库到仓库 $i$ 的距离，也就是
$$\sum_{k=j+1}^{i} (b[k] \times (a[i]-a[k]))$$
我们把这个式子拆开，得到
$$\sum_{k=j+1}^{i} (b[k] \times a[i])-\sum_{k=j+1}^{i} (b[k] \times a[k])$$
也就是
$$a[i] \times \sum_{k=j+1}^{i} b[k]-\sum_{k=j+1}^{i} (b[k] \times a[k])$$
我们只需要把 $b[i]$ 和 $a[i] \times b[i]$ 分别前缀和，就可以解决这个问题了。
但是，如果你打的是裸的斜率优化，你就会发现神奇的东西 $\color{red}{\text{Unaccepted 100}}$。为什么？这 hack 数据是真的神，因为我们忽略了一点，如果后面有一堆仓库没有成品怎么办。这种情况下我们就不一定要在 $n$ 工厂建仓库了，只需要在第一个有成品的工厂之后建即可。
## code
```cpp
#include<cstdio>
#include<algorithm>
using namespace std;
typedef long long ll;
const int N=3000001;
int n,a[N],b[N],c[N],Q[N*2],T=0,R=0;
ll d[N],e[N],f[N];
ll abc(int x,int y)
{
    if(d[x]==d[y]) return 1e18;
    return ((f[x]+e[x])-(f[y]+e[y]))/(d[x]-d[y]);
}
int main()
{
    scanf("%d",&n);
    for(int i=1;i<=n;++i)
    {
        scanf("%d%d%d",&a[i],&b[i],&c[i]);
        d[i]=d[i-1]+b[i];
        e[i]=e[i-1]+a[i]*b[i];
    }
    for(int i=1;i<=n;++i)
    {
        while(T<R&&abc(Q[T],Q[T+1])<a[i]) ++T;
        f[i]=f[Q[T]]+a[i]*(d[i]-d[Q[T]])-(e[i]-e[Q[T]])+c[i];
        while(T<R&&abc(Q[R-1],Q[R])>abc(Q[R],i)) --R;
        Q[++R]=i;
    }
    ll s=1e18;
    for(int i=n;i>=1;--i)
    {
        s=min(s,f[i]);
        if(b[i]) break;
    }
    printf("%lld",s);
    return 0;
}
```
