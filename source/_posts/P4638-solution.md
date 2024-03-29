---
title: 'P4638 [SHOI2011]银行家'
date: 2022-03-08 13:27:00
tags: [网络流,最大流]
description: ' '
---

[P4638 [SHOI2011]银行家](https://www.luogu.com.cn/problem/P4638)
## 分析
这道题题目十分的冗杂，我们先来整理一下题意：一些客户要来银行取金币，如果 $i$ 个客户需要 $c_i$ 个金币，能打开的所有保险箱中金币总数多于 $c_i$ 就会取走对应的金币数，否则会全部取走，而且一个客户来取金币后就可以交换他打开的保险箱的金币，请问所有客户最多可以取走多少金币。
这道题初看就可以发现是一道网络流的题，金币即为流。在每一个客户打开了几个保险箱之后就把这些保险箱合并成一个保险箱即可。但是对于特定的客户（也就是第一个打开这些保险箱的客户之后的客户）才可以合并保险箱，而其他的客户就不行。如果我们把这些保险箱简单地连在一起，就可以给所有客户交换金币。
由于在第一个打开这个保险箱之后的客户才能合并保险箱，我们可以把合并起来的保险箱指向后面的所有客户，而不指向之前的客户就可以了。但是处理合并保险箱也比较麻烦，比如第一个人打开了保险箱 $1,2$，第二个人打开了 $2,3$，此时 $2,3$ 保险箱都可以取到保险箱 $1$ 的金币了，所以我们需要将每一个保险箱对应的合并保险箱再合并起来才对，而不是单独的原始保险箱。而且注意，此时对于保险箱 $2,3$ 来说 $1,2,3$ 是一个保险箱，但是对于保险箱 $1$ 来说只有 $1,2$ 是一个保险箱。
由于一个客户打开的所有保险箱都要合并，我们就可以把这个合并保险箱与这个客户捆绑。如果一个保险箱已经与一个客户捆绑了，那么当前客户就可以把这个保险箱里的金币从上一个人接手全部或部分过来。如果这个保险箱还没有人开过就直接把原始保险箱中的金币放在自己对应的保险箱中。
我们来看一眼样例：
```
3 3
3 1 10
2 1 2 2
2 1 3 3
1 2 6
```
样例建出来的图如下（每一条边的容量都是正无穷），再加上源汇点即可。
![1](/post-images/p4638-solution-1.png?400x)
## code
```cpp
#include<cstdio>
#include<algorithm>
#include<queue>
using namespace std;
typedef long long ll;
int N,n,m,p=1,s1,s2,t[10001],t0[10001],f[10001],g[10001],v[10001];
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
    scanf("%d%d",&m,&n);
    N=n+m+2;
    s1=N-1;
    s2=N;
    for(int i=1;i<=m;++i)
    {
        ll r;
        scanf("%lld",&r);
        road(s1,i,r);
        road(i,s1,0);
    }
    for(int i=1;i<=n;++i)
    {
        int d;
        scanf("%d",&d);
        for(int j=1;j<=d;++j)
        {
            int x;
            scanf("%d",&x);
            if(v[x]==0)
            {
                road(x,m+i,1e18);
                road(m+i,x,0);
            }
            else
            {
                road(m+v[x],m+i,1e18);
                road(m+i,m+v[x],0);
            }
            v[x]=i;
        }
        ll r;
        scanf("%lld",&r);
        road(m+i,s2,r);
        road(s2,m+i,0);
    }
    bfs();
    ll r=0;
    while(u==true)
    {
        for(int i=1;i<=N;++i) t0[i]=t[i];
        r+=dfs(s1,1e18);
    }
    printf("%lld",r);
    return 0;
}
```