---
title: 'P2763 试题库问题'
date: 2022-04-16 12:34:23
tags: [网络流,最大流]
description: ' '
---

[P2763 试题库问题](https://www.luogu.com.cn/problem/P2763)
## 分析
一道网络流问题，我们把每道题和所属类型相连，容量为 $1$，再将源点和每道题相连，容量为 $1$，汇点和每个类型相连，容量为地 $i$ 个类型的题数，如果跑出来的最大流和总题数相同，表示每个类型都有足够的题数，也就是有一个合法方案。
在跑完网络流后，要输出结果，我们可以观察残留网络。一条边有流量流过，表示这条边起点表示的题目被作为这条边终点表示的类型选中了。对于每个类型，找到它的出边，如果有剩余容量，表示其反向边有流量流过。当然每个类型的出边还有到汇点的边，但是这条边一定没有剩余容量，不用考虑。
## code
```cpp
#include<cstdio>
#include<algorithm>
#include<queue>
using namespace std;
typedef long long ll;
int n,m,q=0,N,p=1,s1,s2,t[10001],t0[10001],f[10001],g[10001];
bool u=true;
struct str
{
    int m,q;
    ll r;
}a[1000001];
void road(int x,int y,ll r)
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
            if(f[a[i].m]==0)
            {
                f[a[i].m]=f[k]+1;
                ++g[f[a[i].m]];
                Q.push(a[i].m);
            }
        }
    }
}
ll dfs(int x,ll r)
{
    if(x==s2) return r;
    ll s=0;
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
    scanf("%d%d",&n,&m);
    N=n+m+2;
    s1=N-1;
    s2=N;
    for(int i=1;i<=m;++i)
    {
        road(s1,i,1);
        road(i,s1,0);
    }
    for(int i=1;i<=n;++i)
    {
        ll r;
        scanf("%lld",&r);
        q+=r;
        road(m+i,s2,r);
        road(s2,m+i,0);
    }
    for(int i=1;i<=m;++i)
    {
        int d;
        scanf("%d",&d);
        for(int j=1;j<=d;++j)
        {
            int x;
            scanf("%d",&x);
            road(i,m+x,1);
            road(m+x,i,0);
        }
    }
    bfs();
    ll r=0;
    while(u==true)
    {
        for(int i=1;i<=N;++i) t0[i]=t[i];
        r+=dfs(s1,1e18);
    }
    if(r<q)
    {
        printf("No Solution!");
        return 0;
    }
    for(int i=1;i<=n;++i)
    {
        printf("%d:",i);
        for(int j=t[m+i];j!=0;j=a[j].q)
        {
            if(a[j].r==1)
            {
                printf(" %d",a[j].m);
            }
        }
        printf("\n");
    }
    return 0;
}
```