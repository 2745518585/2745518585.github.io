---
title: 'P2740 [USACO4.2]草地排水Drainage Ditches'
date: 2022-04-13 13:43:36
tags: [网络流,最大流]
description: ' '
---

[P2740 [USACO4.2]草地排水Drainage Ditches](https://www.luogu.com.cn/problem/P2740)
## 分析
最大流的模板。
## code
```cpp
#include<cstdio>
#include<queue>
#include<algorithm>
using namespace std;
typedef long long ll;
int n,m,p=1,t[10001],f[10001];
struct str
{
    int m,q;
    ll w;
}a[10000001];
void road(int x,int y,ll w)
{
    a[++p].m=y;
    a[p].w=w;
    a[p].q=t[x];
    t[x]=p;
}
bool bfs()
{
    queue<int> q;
    q.push(1);
    for(int i=1;i<=n;++i) f[i]=0;
    f[1]=1;
    while(!q.empty())
    {
        int k=q.front();
        q.pop();
        for(int i=t[k];i!=0;i=a[i].q)
        {
            if(f[a[i].m]==0&&a[i].w>0)
            {
                f[a[i].m]=f[k]+1;
                q.push(a[i].m);
            }
        }
    }
    if(f[n]!=0) return true;
    return false;
}
ll dfs(int x,ll w)
{
    if(x==n) return w;
    ll v=0;
    for(int i=t[x];i!=0&&w!=0;i=a[i].q)
    {
        if(f[a[i].m]==f[x]+1&&a[i].w>0)
        {
            ll z=dfs(a[i].m,min(w,a[i].w));
            a[i].w-=z;
            a[i^1].w+=z;
            w-=z;
            v+=z;
        }
    }
    if(v==0) f[x]=0;
    return v;
}
int main()
{
    scanf("%d%d",&m,&n);
    for(int i=1;i<=m;++i)
    {
        int x,y;
        ll w;
        scanf("%d%d%lld",&x,&y,&w);
        road(x,y,w);
        road(y,x,0);
    }
    ll w=0;
    while(bfs()==true)
    {
        w+=dfs(1,1e18);
    }
    printf("%lld",w);
    return 0;
}
```