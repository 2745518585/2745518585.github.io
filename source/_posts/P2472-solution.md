---
title: 'P2472 [SCOI2007] 蜥蜴'
date: 2022-04-16 19:39:42
tags: [网络流,最大流]
description: ' '
---

[P2472 [SCOI2007] 蜥蜴](https://www.luogu.com.cn/problem/P2472)
## 分析
这道题同样也要拆点，每个点最多能经过的蜥蜴数就是它的高度，拆点后中间边的流量设为这个石柱的高度即可。剩下的就枚举两个合法石柱并建边，把源点和有蜥蜴的点相连，容量为 $1$，把能跳出地图的点与汇点相连。
## code
```cpp
#include<cstdio>
#include<algorithm>
#include<queue>
using namespace std;
int m,n,N,d,o,p=1,s1,s2,t[10001],t0[10001],f[10001];
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
bool bfs()
{
    queue<int> Q;
    Q.push(s1);
    for(int i=1;i<=N;++i) f[i]=0;
    f[s1]=1;
    while(!Q.empty())
    {
        int k=Q.front();
        Q.pop();
        for(int i=t[k];i!=0;i=a[i].q)
        {
            if(f[a[i].m]==0&&a[i].r!=0)
            {
                f[a[i].m]=f[k]+1;
                Q.push(a[i].m);
            }
        }
    }
    return f[s2]!=0;
}
int dfs(int x,int r)
{
    if(x==s2) return r;
    int s=0;
    for(int i=t0[x];i!=0;i=a[i].q)
    {
        t0[x]=i;
        if(f[a[i].m]==f[x]+1&&a[i].r!=0)
        {
            int z=dfs(a[i].m,min(r,a[i].r));
            if(z!=0)
            {
                a[i].r-=z;
                a[i^1].r+=z;
                r-=z;
                s+=z;
            }
            else f[a[i].m]=0;
            if(r==0) return s; 
        }
    }
    return s;
}
int main()
{
    scanf("%d%d%d",&m,&n,&d);
    N=m*n*2+2;
    s1=N-1;
    s2=N;
    for(int i=1;i<=m;++i)
    {
        scanf("\n");
        for(int j=1;j<=n;++j)
        {
            int r=getchar()-'0';
            if(r!=0)
            {
                int a1=(i-1)*n+j;
                road(a1+m*n,a1,r);
                road(a1,a1+m*n,0);
            }
        }
    }
    for(int i=1;i<=m;++i)
    {
        scanf("\n");
        for(int j=1;j<=n;++j)
        {
            char z=getchar();
            if(z=='L')
            {
                ++o;
                int a1=(i-1)*n+j;
                road(s1,a1+m*n,1);
                road(a1+m*n,s1,0);
            }
        }
    }
    for(int i=1;i<=m;++i)
    {
        for(int j=1;j<=n;++j)
        {
            if(i<=d||i>=m-d+1||j<=d||j>=n-d+1)
            {
                int a1=(i-1)*n+j;
                road(a1,s2,1e9);
                road(s2,a1,0);
            }
        }
    }
    for(int i=1;i<=m;++i)
    {
        for(int j=1;j<=n;++j)
        {
            int a1=(i-1)*n+j;
            for(int k=1;k<=m;++k)
            {
                for(int l=1;l<=n;++l)
                {
                    int a2=(k-1)*n+l;
                    if((i-k)*(i-k)+(j-l)*(j-l)<=d*d)
                    {
                        road(a1,a2+m*n,1e9);
                        road(a2+m*n,a1,0);
                    }
                }
            }
        }
    }
    int r=0;
    while(bfs())
    {
        for(int i=1;i<=N;++i) t0[i]=t[i];
        r+=dfs(s1,1e9);
    }
    printf("%d",o-r);
    return 0;
}
```