---
title: 'P3356 火星探险问题'
date: 2022-05-21 13:34:45
tags: [网络流,费用流]
description: ' '
---

[P3356 火星探险问题](https://www.luogu.com.cn/problem/P3356)
## 分析
这道题的方法同 [P4012](/post/P4012-solution/) ，但是还要求输出一种方案，我们就需要知道流量的流动情况。如果一条边有流量流过，那么这条边的反向边容量 $r$ 一定不为 $0$，而且流过的流量就是 $r$，也就是有 $r$ 个探测车经过。我们只需要从源点开始一直走反向边容量不为 $0$ 的边，且每走一次容量减一，注意要判断有没有往回走，还要注意处理拆开的两个点的关系。
## code
```cpp
#include<cstdio>
#include<algorithm>
#include<queue>
using namespace std;
typedef long long ll;
const int N=100001,M=1000001;
int n,m,p=1,s1,s2,t[N],t0[N];
ll v,f[N],q;
bool h[N];
struct road
{
    int m,q;
    ll r,w;
}a[M];
int sum(int x,int y)
{
    return (x-1)*n+y;
}
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
    for(int i=1;i<=s2;++i)
    {
        f[i]=-1e18;
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
            if(a[i].r>0&&f[k]+a[i].w>f[a[i].m])
            {
                f[a[i].m]=f[k]+a[i].w;
                Q.push(a[i].m);
                h[a[i].m]=true;
            }
        }
    }
    if(f[s2]!=-1e18) return true;
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
void dfs0(int x,int k)
{
    if(x==sum(m,n)) return;
    for(int i=t[m*n+x];i!=0;i=a[i].q)
    {
        if(a[i].m==x||a[i^1].r==0) continue;
        ++a[i].r;
        --a[i^1].r;
        if(a[i].m==x+1) printf("%d 1\n",k);
        else printf("%d 0\n",k);
        dfs0(a[i].m,k);
        return;
    }
}
int main()
{
    scanf("%lld%d%d",&q,&n,&m);
    s1=m*n*2+1,s2=m*n*2+2;
    for(int i=1;i<=m;++i)
    {
        for(int j=1;j<=n;++j)
        {
            int x;
            scanf("%d",&x);
            if(x==1) continue;
            if(x==2)
            {
                road(sum(i,j),m*n+sum(i,j),1,1);
                road(m*n+sum(i,j),sum(i,j),0,-1);
            }
            road(sum(i,j),m*n+sum(i,j),1e18,0);
            road(m*n+sum(i,j),sum(i,j),0,0);
            if(i<m)
            {
                road(m*n+sum(i,j),sum(i+1,j),1e18,0);
                road(sum(i+1,j),m*n+sum(i,j),0,0);
            }
            if(j<n)
            {
                road(m*n+sum(i,j),sum(i,j+1),1e18,0);
                road(sum(i,j+1),m*n+sum(i,j),0,0);
            }
        }
    }
    road(s1,sum(1,1),q,0);
    road(sum(1,1),s1,0,0);
    road(m*n+sum(m,n),s2,q,0);
    road(s2,m*n+sum(m,n),0,0);
    ll r=0;
    while(SPFA())
    {
        for(int i=1;i<=s2;++i) 
        {
            t0[i]=t[i];
            h[i]=false;
        }
        r+=dfs(s1,1e18);
    }
    for(int i=1;i<=r;++i)
    {
        dfs0(sum(1,1),i);
    }
    return 0;
}
```