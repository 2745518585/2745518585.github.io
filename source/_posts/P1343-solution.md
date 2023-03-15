---
title: 'P1343 地震逃生'
date: 2022-04-13 13:37:16
tags: [网络流,最大流]
description: ' '
---

[P1343 地震逃生](https://www.luogu.com.cn/problem/P1343)
## 分析
这道题我们把学生看成流量，可以发现这道题就是最大流模板题，注意处理分的批数。
## code
```cpp
#include<cmath>
#include<cstdio>
#include<queue>
using namespace std;
int n,m,p=1,t[10001],f[10001];
struct str
{
    int m,q,w;
}a[100001];
void road(int x,int y,int w)
{
    a[++p].m=y;
    a[p].q=t[x];
    t[x]=p;
    a[p].w=w;
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
int dfs(int x,int w)
{
    if(x==n) return w;
    int s=0;
    for(int i=t[x];i!=0;i=a[i].q)
    {
        if(f[a[i].m]==f[x]+1&&a[i].w>0)
        {
            int z=dfs(a[i].m,min(w,a[i].w));
            if(z!=0)
            {
                a[i].w-=z;
                a[i^1].w+=z;
                w-=z;
                s+=z;
            }
            else f[a[i].m]=0;
        }
    }
    return s;
}
int main()
{
    int r;
    scanf("%d%d%d",&n,&m,&r);
    for(int i=1;i<=m;++i)
    {
        int x,y,w;
        scanf("%d%d%d",&x,&y,&w);
        road(x,y,w);
        road(y,x,0);
    }
    int s=0;
    while(bfs()==true)
    {
        s+=dfs(1,1e9);
    }
    if(s==0) printf("Orz Ni Jinan Saint Cow!");
    else printf("%d %d",s,int(ceil(r*1.0/s)));
}
```