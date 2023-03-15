---
title: '最小费用最大流Dinic'
date: 2022-04-20 14:50:20
tags: [网络流,费用流]
description: ' '
---

## 前言
模板题：[P3381 【模板】最小费用最大流](https://www.luogu.com.cn/problem/P3381)
[费用流题单](/post/minimum-cost-maximum-flow-problemlist/)
前置知识：[最小费用最大流Dinic](/post/minimum-cost-maximum-flow-EK/)
我们已经学习了用 EK 解决费用流的问题，但是 EK 每一次增广都要一次 SPFA，效率低，于是我们想，费用流能不能用 Dinic 呢？答案是肯定的。
## 基本思路
最大流的 Dinic 就是用一次 bfs 来分层，然后进行 dfs，而费用流的 EK 用的是多次 SPFA，如果我们把 Dinic 中的 bfs 换成 SPFA 就可以了，只需要判断这条边是否在最短路图中，也就起点到这个点的距离加上边权等于下一个点到起点的距离。但是有一个问题，最大流中的分层图是不存在环路的，但是最短路图就有可能，就有可能死循环并 $\color{black}{\text{MLE}}$，我们需要用一个数组来判断一个点是否走过，到达这个点标记真，离开后标记为假。
## code
```cpp
#include<cstdio>
#include<algorithm>
#include<queue>
using namespace std;
typedef long long ll;
const int N=10001,M=100001;
int n,m,p=1,v,s1,s2,t[N],t0[N];
ll f[N];
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
    for(int i=1;i<=n;++i)
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
    scanf("%d%d%d%d",&n,&m,&s1,&s2);
    for(int i=1;i<=m;++i)
    {
        int x,y;
        ll r,w;
        scanf("%d%d%lld%lld",&x,&y,&r,&w);
        road(x,y,r,w);
        road(y,x,0,-w);
    }
    ll r=0;
    while(SPFA())
    {
        for(int i=1;i<=n;++i) 
        {
            t0[i]=t[i];
            h[i]=false;
        }
        r+=dfs(s1,1e18);
    }
    printf("%lld %lld",r,v);
    return 0;
}
```