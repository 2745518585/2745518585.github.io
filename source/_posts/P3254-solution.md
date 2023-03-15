---
title: 'P3254 圆桌问题'
date: 2022-04-13 13:51:56
tags: [网络流,最大流]
description: ' '
---

[P3254 圆桌问题](https://www.luogu.com.cn/problem/P3254)
## 分析
这道题也比较容易发现是一道网络流，我们来考虑如何建图。左边 $m$ 个点代表每个单位，右边 $n$ 个点代表每个圆桌，每一个单位在一个圆桌只能有 $1$ 个人，也就是从每个单位到每个圆桌的边容量为 $1$。然后将源点与每个单位相连，容量为 $r_i$，将每个圆桌与汇点相连，容量为 $c_i$。最后跑一边最大流，得到的最大流就是答案。
由于有很多点，初始化的时候注意要初始化全部的点。
## code
```cpp
#include<cstdio>
#include<algorithm>
#include<queue>
using namespace std;
int n,m=0,m1,m2,p=1,s1,s2,t[10001],t0[10001],f[10001],g[10001];
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
    scanf("%d%d",&m1,&m2);
    n=m1+m2+2;
    s1=1;
    s2=n;
    for(int i=1;i<=m1;++i)
    {
        int r;
        scanf("%d",&r);
        m+=r;
        road(1,i+1,r);
        road(i+1,1,0);
    }
    for(int i=1;i<=m2;++i)
    {
        int r;
        scanf("%d",&r);
        road(i+m1+1,n,r);
        road(n,i+m1+1,0);
    }
    for(int i=1;i<=m1;++i)
    {
        for(int j=1;j<=m2;++j)
        {
            road(i+1,j+m1+1,1);
            road(j+m1+1,i+1,0);
        }
    }
    bfs();
    int r=0;
    while(u==true)
    {
        for(int i=1;i<=n;++i) t0[i]=t[i];
        r+=dfs(s1,1e9);
    }
    if(r!=m)
    {
        printf("0");
        return 0;
    }
    printf("1\n");
    for(int i=1;i<=m1;++i)
    {
        for(int j=t[i+1];j!=0;j=a[j].q)
        {
            if(a[j].m!=1&&a[j].r==0)
            {
                printf("%d ",a[j].m-m1-1);
            }
        }
        printf("\n");
    }
    return 0;
}
```