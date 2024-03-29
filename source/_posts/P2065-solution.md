---
title: 'P2065 [TJOI2011]卡片'
date: 2022-03-08 12:38:09
tags: [网络流,最大流]
description: ' '
---

[P2065 [TJOI2011]卡片](https://www.luogu.com.cn/problem/P2065)
## 分析
这道题显然可以用最大流来做，将红色卡片放在一边，蓝色卡片放在另一边，从源点 $s$ 向每个红色卡片连一条容量为 $1$ 的边，从每个蓝色卡片向汇点 $t$ 连一条容量为 $1$ 的边，并把所有公约数不为 $1$ 的异色卡片用一条容量为 $1$ 的边连起来，再从源点向汇点跑最大流即可。这道题看似可以这样简单地建图，但是这样其实会 $\color{purple}{TLE}$ ，只有 $70$ 分。分析一下，我们总共有 $n\times m$ 条边，每一次都要跑一边最大公约数，这些数最大是 $1000000$ ，很容易被卡掉。我们需要一个新的建边方法。
![1](/post-images/P2065-solution-1.png?500x)
我们之前的方法需要 $n\times m$ 条边，如果能找到一种 $n+m$ 次的建边就可以了。建边的标准是两个数的最大公约数不为 $1$ ，也就是两个数存在同样的质因数。这 $n+m$ 个数中对于异色的卡片，所有有质因数 $2$ 的数都相互匹配，有质因数 $3$ 的数都可以相互匹配，等等。如果我们把有质因数 $2$ 的连在一个点上，有质因数 $3$ 的连在一个点上，依此类推，只需要 $n+m$ 次分解质因数即可。其中红色卡片与质因数点的边由红色卡片指向质因数点，蓝色卡片与质因数点的边由质因数点指向蓝色卡片，再连接源汇点就可以了。如图：
![2](/post-images/P2065-solution-2.png?600x)
## code
```cpp
#include<cstdio>
#include<algorithm>
#include<queue>
#include<cmath>
using namespace std;
int N,n1,n2,q=0,p=1,s1,s2,t[10001],t0[10001],f[10001],g[10001],d[10000001];
bool u=true;
struct str
{
    int m,q,r;
}a[1000001];
int gcd(int a,int b)
{
    if(b==0) return a;
    return gcd(b,a%b);
}
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
            if(f[a[i].m]==0)
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
void ss1(int k,int x)
{
    for(int i=2;i<=sqrt(x)&&x!=1;++i)
    {
        if(x%i==0)
        {
            if(d[i]==0) d[i]=++q;
            road(k,n1+n2+d[i],1);
            road(n1+n2+d[i],k,0);
        }
        while(x%i==0) x/=i;
    }
    if(x>1)
    {
        if(d[x]==0) d[x]=++q;
        road(k,n1+n2+d[x],1);
        road(n1+n2+d[x],k,0);
    }
}
void ss2(int k,int x)
{
    for(int i=2;i<=sqrt(x)&&x!=1;++i)
    {
        if(x%i==0)
        {
            if(d[i]==0) d[i]=++q;
            road(n1+n2+d[i],k,1);
            road(k,n1+n2+d[i],0);
        }
        while(x%i==0) x/=i;
    }
    if(x>1)
    {
        if(d[x]==0) d[x]=++q;
        road(n1+n2+d[x],k,1);
        road(k,n1+n2+d[x],0);
    }
}
void abc()
{
    scanf("%d%d",&n1,&n2);
    p=1;
    for(int i=1;i<=N;++i) f[i]=g[i]=t[i]=0;
    u=true;
    for(int i=1;i<=n1;++i)
    {
        int x;
        scanf("%d",&x);
        ss1(i,x);
    }
    for(int i=1;i<=n2;++i)
    {
        int x;
        scanf("%d",&x);
        ss2(n1+i,x);
    }
    N=n1+n2+q+2;
    s1=N-1;
    s2=N;
    for(int i=1;i<=n1;++i)
    {
        road(s1,i,1);
        road(i,s1,0);
    }
    for(int i=1;i<=n2;++i)
    {
        road(n1+i,s2,1);
        road(s2,n1+i,0);
    }
    bfs();
    int r=0;
    while(u==true)
    {
        for(int i=1;i<=N;++i) t0[i]=t[i];
        r+=dfs(s1,1e18);
    }
    printf("%d\n",r);
    return;
}
int main()
{
    int T;
    scanf("%d",&T);
    while(T--) abc();
    return 0;
}
```