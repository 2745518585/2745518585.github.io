---
title: 'P4014 分配问题'
date: 2022-05-10 13:19:27
tags: [网络流,费用流]
description: ' '
---

[P4014 分配问题](https://www.luogu.com.cn/problem/P4014)
## 分析
这道题其实是 [P4015](/post/P4015-solution/) 的简化版，P4015 是仓库中可以有多个货物，商店也可能需要多个货物，而这道题中工件最多 $1$ 个，工人也只能做 $1$ 个工件。所以只需要把上一题所有的边流量变为 $1$，然后跑最大费用最大流即可。
## code
```cpp
#include<cstdio>
#include<algorithm>
#include<queue>
using namespace std;
int n,s1,s2,p=1,t[10001],f[10001],g[10001],r[10001];
bool h[10001];
struct str
{
    int x,m,q,r1,r2,w;
}a[100001];
void road(int x,int y,int r,int w)
{
    a[++p].x=x;
    a[p].m=y;
    a[p].q=t[x];
    t[x]=p;
    a[p].r1=r;
    a[p].r2=r;
    a[p].w=w;
}
bool SPFA()
{
    queue<int> q;
    q.push(s1);
    for(int i=1;i<=n*2+2;++i)
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
            if(a[i].r1>0&&g[k]+a[i].w<g[a[i].m])
            {
                f[a[i].m]=min(f[k],a[i].r1);
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
bool SPFA2()
{
    queue<int> q;
    q.push(s1);
    for(int i=1;i<=n*2+2;++i)
    {
        f[i]=1e9;
        g[i]=-1e9;
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
            if(a[i].r2>0&&g[k]+a[i].w>g[a[i].m])
            {
                f[a[i].m]=min(f[k],a[i].r2);
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
    scanf("%d",&n);
    s1=1;
    s2=n*2+2;
    for(int i=1;i<=n;++i)
    {
        road(1,i+1,1,0);
        road(i+1,1,0,0);
    }
    for(int i=1;i<=n;++i)
    {
        road(i+n+1,n*2+2,1,0);
        road(n*2+2,i+n+1,0,0);
    }
    for(int i=1;i<=n;++i)
    {
        for(int j=1;j<=n;++j)
        {
            int w;
            scanf("%d",&w);
            road(i+1,j+n+1,1,w);
            road(j+n+1,i+1,0,-w);
        }
    }
    int w1=0,w2=0;
    while(SPFA())
    {
        w1+=f[s2]*g[s2];
        int x=s2;
        while(x!=s1)
        {
            a[r[x]].r1-=f[s2];
            a[r[x]^1].r1+=f[s2];
            x=a[r[x]].x;
        }
    }
    while(SPFA2())
    {
        w2+=f[s2]*g[s2];
        int x=s2;
        while(x!=s1)
        {
            a[r[x]].r2-=f[s2];
            a[r[x]^1].r2+=f[s2];
            x=a[r[x]].x;
        }
    }
    printf("%d\n%d",w1,w2);
    return 0;
}
```