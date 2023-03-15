---
title: '最大流ISAP'
date: 2022-02-18 12:35:04
tags: [网络流,最大流]
description: ' '
---

## 前言
模板题：[P3376 【模板】网络最大流](https://www.luogu.com.cn/problem/P3376)
[最大流题单](/post/maximum-flow-problemlist/)
前置知识：[最大流Dinic](/post/maximum-flow-Dinic/)
Dinic 算法已经能够很好地解决最大流问题了，但是它还是有一个弊端：会进行多次且次数不确定的 bfs，就会浪费时间，还可能被毒瘤出题人卡。Dinic 中，bfs 的作用就是将这个图分层，如果我们只在一开始时将图分层，在之后的 dfs 中调整每个点的高度，就可以大大减少bfs次数。
## ISAP
ISAP 就成功地优化了 Dinic。不过 ISAP 是将汇点的高度设为 $1$，源点最高，流量会从高度较高的点流到高度较低的点，原因会在后面解释。在 dfs 过程中，如果一个点仍有剩余流量却流不出去了，那么就把这个点的高度加一，这样它才能流到更多的点。如果汇点高度最高，就需要其它点的高度减一，就可能出现负数，最好避免这种情况。这样就可以避免多次分层浪费时间。
但是 ISAP 不是在找不到汇点的时候结束，而是在出现断层的时候停止。断层指的是汇点的高度到源点的高度之间有一个高度没有点。由于流量都是从一层流向下一层，如果有一个高度没有点，流量就无法到达下一层，也就无法到达汇点，就可以直接停止算法。由于一开始高度是连续的，只要有剩余流量，就会增加高度，没有流量就不会，所以这个图在出现断层时一定是上一个层的所有点没有剩余流量了。
同样，ISAP 也可以添加当前弧优化，具体见最大流 Dinic 算法。
## code
``` cpp
#include<cstdio>
#include<algorithm>
#include<queue>
using namespace std;
int n,m,p=1,s1,s2,t[10001],t0[10001],f[10001],g[10001];
bool u=true;
struct str
{
    int m,q,r;
}a[1000001];
void road(int x,int y,int r)
{
    a[++p].m=y;
    a[p].q=t[x];
    t[x]=p;
    a[p].r=r;
}
void bfs()
{
    queue<int> Q;
    Q.push(s2);
    f[s2]=1;
    g[1]=1;
    while(!Q.empty())
    {
        int k=Q.front();
        Q.pop();
        for(int i=t[k];i!=0;i=a[i].q)
        {
            if(f[a[i].m]==0&&a[i].r==0)
            {
                f[a[i].m]=f[k]+1;
                ++g[f[a[i].m]];
                Q.push(a[i].m);
            }
        }
    }
}
int dfs(int x,int r)
{
    if(x==s2) return r;
    int s=0;
    for(int i=t0[x];i!=0;i=a[i].q)
    {
        t0[x]=i;
        if(f[x]==f[a[i].m]+1&&a[i].r!=0)
        {
            int z=dfs(a[i].m,min(r,a[i].r));
            if(z!=0)
            {
                a[i].r-=z;
                a[i^1].r+=z;
                r-=z;
                s+=z;
            }
            if(r==0) return s;
        }
    }
    --g[f[x]];
    if(g[f[x]]==0) u=false;
    ++f[x];
    ++g[f[x]];
    return s;
}
int main()
{
    scanf("%d%d%d%d",&n,&m,&s1,&s2);
    for(int i=1;i<=m;++i)
    {
        int x,y,r;
        scanf("%d%d%d",&x,&y,&r);
        road(x,y,r);
        road(y,x,0);
    }
    bfs();
    int r=0;
    while(u==true)
    {
        for(int i=1;i<=n;++i) t0[i]=t[i];
        r+=dfs(s1,1e9);
    }
    printf("%d",r);
    return 0;
}
```