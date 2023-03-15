---
title: '最小费用最大流EK'
date: 2022-02-19 13:14:26
tags: [网络流,费用流]
description: ' '
---

 ## 前言
模板题：[P3381 【模板】最小费用最大流](https://www.luogu.com.cn/problem/P3381)
[费用流题单](/post/minimum-cost-maximum-flow-problemlist/)
前置知识：[最大流Dinic](/post/maximum-flow-Dinic/)
进阶知识：[最小费用最大流Dinic](/post/minimum-cost-maximum-flow-Dinic/)
最小费用最大流问题，简称费用流。费用流问题是在最大流的基础上给每条边添加一个边权，也就是费用，总费用就是流过每条边的流量乘上费用的和，在保证流量最大的情况下，求出这个最小费用。
最大流问题可以使用每次 dfs 找增广路来解决，费用流就可以用 SPFA 来寻找增广路，这样就可以保证每一次的增广路的费用最小。由于最大流算法可以“反悔”，所以即使当前的增广路并不是全局最优的，也可以在之后的增广中“反悔”，所以最后找到的一定是最优解。不过 SPFA 在搜索过程中不知道最优解，所以要记录最优路线，搜索完成后再更新所有边的容量。
## SPFA
SPFA 的过程中总共需要维护 $3$ 个数组：到达每个点的流量，到达每个点的费用，与这个点是由哪个点更新来的。不过SPFA是找到费用最小的增广路，而不是流量最大，因为可以进行多次 SPFA 增加流量。如果当前边的流量不为 $0$ ，到达每个点的最小费用 $g$ 的就可以做如下更新： $g[a[i].m]=\max(g[a[i].m],g[x]))$。如果最小费用更新成功，流量 $f[a[i].m]$ 也要更新为 $min(f[x]),a[i].r$ ，这个点的父亲 $r[a[i].m]$ 则更新为 $x$。这样就可以找到当前费用最小的增广路。当汇点没有被走到的时候，就说明不存在增广路了。
进行完一次 SPFA 后，最大流量就要加上汇点的流量 $f[t]$，费用加上汇点的流量与到达汇点的费用之积 $f[t] \times g[t]$
在进行了 SPFA 之后，我们就需要更新每条边的容量。从汇点开始，每一次都到达当前点的父亲，并把经过的所有边的容量减去 $f[t]$，其相反边的容量加上 $f[s]$。
## code
``` cpp
#include<cstdio>
#include<algorithm>
#include<queue>
using namespace std;
int n,m,s1,s2,p=1,t[10001],f[10001],g[10001],r[10001];
bool h[10001];
struct str
{
    int x,m,q,r,w;
}a[100001];
void road(int x,int y,int r,int w)
{
    a[++p].x=x;
    a[p].m=y;
    a[p].q=t[x];
    t[x]=p;
    a[p].r=r;
    a[p].w=w;
}
bool SPFA()
{
    queue<int> q;
    q.push(s1);
    for(int i=1;i<=n;++i)
    {
        f[i]=1e9;
        g[i]=1e9;
        h[i]=false;
        r[i]=0;
    }
    g[s1]=0;
    h[s1]=true;
    while(!q.empty())
    {
        int k=q.front();
        q.pop();
        if(h[k]==false) continue;
        h[k]=false;
        for(int i=t[k];i!=0;i=a[i].q)
        {
            if(a[i].r>0&&g[k]+a[i].w<g[a[i].m])
            {
                f[a[i].m]=min(f[k],a[i].r);
                g[a[i].m]=g[k]+a[i].w;
                r[a[i].m]=i;
                q.push(a[i].m);
                h[a[i].m]=true;
            }
        }
    }
    if(f[s2]!=1e9) return true;
    return false;
}
int main()
{
    scanf("%d%d%d%d",&n,&m,&s1,&s2);
    for(int i=1;i<=m;++i)
    {
        int x,y,r,w;
        scanf("%d%d%d%d",&x,&y,&r,&w);
        road(x,y,r,w);
        road(y,x,0,-w);
    }
    int s=0,w=0;
    while(SPFA())
    {
        s+=f[s2];
        w+=f[s2]*g[s2];
        int x=s2;
        while(x!=s1)
        {
            a[r[x]].r-=f[s2];
            a[r[x]^1].r+=f[s2];
            x=a[r[x]].x;
        }
    }
    printf("%d %d",s,w);
    return 0;
}
```