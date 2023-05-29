---
title: 'P2754 [CTSC1999]家园 / 星际转移问题'
date: 2022-04-16 18:36:23
tags: [网络流,最大流]
description: ' '
alias: P2754-solution/index.html
---

[P2754 [CTSC1999]家园 / 星际转移问题](https://www.luogu.com.cn/problem/P2754)
## 分析
这道题其实也可以用网络流来做，每一个太空站（地球、月球）在每个时间都建一个点。由于人可以在太空站上停留，所以每一个点都要向下一个时间的对应点连边，容量为正无穷。对于每个太空船将当前时间的起点和终点连接起来，容量为太空船的承载能力。建出来的图如下：
![1](/post-images/P2754-solution-1.png)
（图片来自网络）
但是我们只能在确定时间的情况下求出最多的转移人数，无法直接求出最少天数，必须二分。但是还有另一个方法，假设我们已经跑了时间在 $m$ 以内的图，得到了最大流和残留网络，如果我们加上 $m+1$ 时间的点和边，再在残留网络上跑最大流，将得到的最大流加在原来的最大流上，是不是就可以得到 $m+1$ 天的转移人数了？因为这相当于我们已经找到了一些増广路，继续寻找更多増广路的过程。这样我们跑出 $1\sim m$ 时间的最大转移人数，只相当于跑了一遍时间 $m$ 的图上的最大流。对于无解情况，我们只需要选一个比较大的数，如果都跑不出来就判无解即可。
## code
```cpp
#include<cstdio>
#include<algorithm>
#include<queue>
using namespace std;
int N,M=200,n,m,q,p=1,s1,s2,t[10001],t0[10001],f[10001],e[201][501],y[501],z[501];
bool u=true;
struct str
{
    int m,q;
    int r;
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
    scanf("%d%d%d",&n,&m,&q);
    n+=2;
    for(int i=1;i<=m;++i)
    {
        scanf("%d%d",&z[i],&y[i]);
        for(int j=0;j<=y[i]-1;++j)
        {
            scanf("%d",&e[i][j]);
            e[i][j]+=2;
        }
    }
    s1=2;
    int r=0;
    for(int i=1;i<=M;++i)
    {
        N=(i+1)*n;
        s2=i*n+1;
        for(int j=1;j<=n;++j)
        {
            road((i-1)*n+j,i*n+j,1e9);
            road(i*n+j,(i-1)*n+j,0);
        }
        for(int j=1;j<=m;++j)
        {
            road((i-1)*n+e[j][(i-1)%y[j]],i*n+e[j][i%y[j]],z[j]);
            road(i*n+e[j][i%y[j]],(i-1)*n+e[j][(i-1)%y[j]],0);
        }
        while(bfs())
        {
            for(int j=1;j<=N;++j) t0[j]=t[j];
            r+=dfs(s1,1e9);
        }
        if(r>=q)
        {
            printf("%d",i);
            return 0;
        }
    }
    printf("0");
    return 0;
}

```