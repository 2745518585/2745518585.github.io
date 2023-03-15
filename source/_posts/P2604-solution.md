---
title: 'P2604 [ZJOI2010]网络扩容'
date: 2022-05-10 13:25:28
tags: [网络流,费用流]
description: ' '
---

[P2604 [ZJOI2010]网络扩容](https://www.luogu.com.cn/problem/P2604)
## 分析
我们先来分析一下这个问题：我们要在一个图中跑费用流，当一条边流量在 $c$ 以下时，费用为 $0$，当这条边容量超过 $c$ 时，超过部分的费用为 $w$。我们可以发现，图中每一条边都有 $2$ 种计费方式，而且是分段的一次函数。这时候我们要用到经典的“拆边”，将这条边拆成 $2$ 条边，一条边的容量为 $c$，费用为 $0$，另一条边容量为 $inf$，费用为 $w$。如果这条边的流量不超过 $c$，那么为了费用最小，所有流量都会从第 $1$ 条边流过。而如果流量超过 $c$，多余的部分就会从第 $2$ 条边流过，且多余部分费用为 $w$。这种方法有时非常好用，但是仅限于此分段函数的斜率单调不下降的时候，也就是费用逐渐变大，否则无法使用（在最大费用最大流时相反）。
注意在跑完第一遍最大流之后的初始化。
## code
```cpp
#include<cstdio>
#include<algorithm>
#include<queue>
using namespace std;
typedef long long ll;
const int N=10001,M=100001;
int n,m,r0,p=1,v,s1,s2,t[N],t0[N];
ll f[N],b[N][4];
bool h[N];
struct str
{
    int m,q;
    ll r,w;
}a[M];
void road(int x,int y,ll r,ll w)
{
    a[++p].m=y;
    a[p].q=t[x];
    t[x]=p;
    a[p].r=r;
    a[p].w=w;
}
bool SPFA()
{
    queue<int> Q;
    Q.push(s1);
    for(int i=1;i<=n+1;++i)
    {
        f[i]=1e18;
        h[i]=false;
    }
    f[s1]=0;
    h[s1]=true;
    while(!Q.empty())
    {
        int k=Q.front();
        Q.pop();
        if(h[k]==false) continue;
        h[k]=false;
        for(int i=t[k];i!=0;i=a[i].q)
        {
            if(a[i].r>0&&f[k]+a[i].w<f[a[i].m])
            {
                f[a[i].m]=f[k]+a[i].w;
                Q.push(a[i].m);
                h[a[i].m]=true;
            }
        }
    }
    if(f[s2]!=1e18) return true;
    return false;
}
ll dfs(int x,ll r)
{
    if(x==s2) return r;
    ll s=0;
    for(int i=t0[x];i!=0;i=a[i].q)
    {
        t0[x]=i;
        if(h[a[i].m]==false&&a[i].r>0&&f[a[i].m]==f[x]+a[i].w)
        {
            h[a[i].m]=true;
            ll z=dfs(a[i].m,min(r,a[i].r));
            h[a[i].m]=false;
            if(z!=0)
            {
                a[i].r-=z;
                a[i^1].r+=z;
                r-=z;
                s+=z;
                v+=z*a[i].w;
            }
            else f[a[i].m]=0;
            if(r==0) return s; 
        }
    }
    return s;
}
int main()
{
    scanf("%d%d%d",&n,&m,&r0);
    s1=1,s2=n;
    for(int i=1;i<=m;++i)
    {
        int x,y;
        ll r,w;
        scanf("%d%d%lld%lld",&x,&y,&r,&w);
        b[i][0]=x,b[i][1]=y,b[i][2]=r,b[i][3]=w;
        road(x,y,r,0);
        road(y,x,0,0);
    }
    ll r=0;
    while(SPFA())
    {
        for(int i=1;i<=n+1;++i) 
        {
            t0[i]=t[i];
            h[i]=false;
        }
        r+=dfs(s1,1e18);
    }
    printf("%lld ",r);
    s1=n+1;
    road(s1,1,r0,0);
    road(1,s1,0,0);
    for(int i=1;i<=m;++i)
    {
        road(b[i][0],b[i][1],1e18,b[i][3]);
        road(b[i][1],b[i][0],0,-b[i][3]);
    }
    v=0;
    while(SPFA())
    {
        for(int i=1;i<=n+1;++i) 
        {
            t0[i]=t[i];
            h[i]=false;
        }
        r+=dfs(s1,1e18);
    }
    printf("%lld",v);
    return 0;
}
```