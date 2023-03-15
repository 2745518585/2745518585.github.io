---
title: 'P4360 [CEOI2004]锯木厂选址'
date: 2022-04-05 09:39:38
tags: [动态规划,单调队列,斜率优化dp]
description: ' '
---

[P4360 [CEOI2004]锯木厂选址](https://www.luogu.com.cn/problem/P4360)
## 分析
这道题与仓库建设十分相似，只是不需要一定在 $n$ 处建立锯木厂。由于只有 $2$ 个锯木厂，我们只需要先预处理好第一个锯木厂的情况，再斜率优化求出第二个锯木厂。把前两个锯木厂放好后，枚举第二个锯木厂的位置再加上剩下的木材运到山脚下的费用取最小值即可。
## code
```cpp
#include<cstdio>
#include<algorithm>
using namespace std;
typedef long long ll;
const int N=1000001;
int n,Q[N*2],T=0,R=0;
ll a[N],b[N],c[N],f[N],g[N];
ll abc(int x,int y)
{
    if(a[x]==a[y]) return 1e18;
    return ((g[x]+c[x])-(g[y]+c[y]))/(a[x]-a[y]);
}
int main()
{
    scanf("%d",&n);
    for(int i=1;i<=n;++i)
    {
        int x,y;
        scanf("%d%d",&x,&y);
        a[i]=a[i-1]+x;
        b[i+1]=b[i]+y;
        c[i]=c[i-1]+x*b[i];
        g[i]=b[i]*a[i]-c[i];
    }
    for(int i=1;i<=n;++i)
    {
        while(T<R&&abc(Q[T],Q[T+1])<b[i]) ++T;
        f[i]=g[Q[T]]+b[i]*(a[i]-a[Q[T]])-(c[i]-c[Q[T]]);
        while(T<R&&abc(Q[R],i)<abc(Q[R-1],Q[R])) --R;
        Q[++R]=i;
    }
    ll s=1e18;
    for(int i=1;i<=n;++i)
    {
        s=min(s,f[i]+b[n+1]*(a[n]-a[i])-(c[n]-c[i]));
    }
    printf("%lld",s);
    return 0;
}
```