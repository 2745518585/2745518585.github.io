---
title: '最大流Dinic'
date: 2022-02-15 11:15:34
tags: [网络流,最大流]
description: ' '
---

## 前言
模板题：[P3376 【模板】网络最大流](https://www.luogu.com.cn/problem/P3376)
[最大流题单](/post/maximum-flow-problemlist/)
进阶知识：[最大流ISAP](/post/maximum-flow-ISAP/)
一个图中，每条边都有一定的承载能力，也就是容量，流过这条边的流量不得超过这条边的最大流量，从源点到汇点的最大流量就叫做最大流。
最大流问题有很多种解法，而 Dinic 是一种简单又比较实用的方法，但是多数解法都有一个思想：残留网络。
## 残留网络
增广路指的就是一条从源点到汇点，且经过的边最小容量不为 $0$ 的路径.显而易见，我们可以通过每一次搜索增广路，然后将经过的边的流量减去这条增广路的流量，这条增广路的流量就是经过的所有边的容量的最小值。但是这种算法是错误的，见下图：
![1](/post-images/maximum-flow-Dinic-1.png?300x)
如果我们按照上述算法，可能就会找到这个路径：
![2](/post-images/maximum-flow-Dinic-2.png?300x)
这样找出来的最大流为 $1$，而真实的最大流为 $2$，显然不正确。这是因为我们第一次找到的增广路影响了后面寻找更多增广路。如果我们让后续寻找更多增广路时可以“反悔”，也就是修改之前的增广路，这个问题就解决了。为了实现这个方案，残留网络就出现了。
我们对于每一条边都增加一条容量为 $0$ 反向边，找到增广路后经过的边流量要减去此增广路的流量，其反向边的流量还要加上此增广路的流量。所有反向边组成的图就是残留网络。于是刚刚的图就变成了这个图：
![3](/post-images/maximum-flow-Dinic-3.png?300x)
![4](/post-images/maximum-flow-Dinic-4.png?300x)
对于这个图，我们还可以找到一条增广路：
![5](/post-images/maximum-flow-Dinic-5.png?300x)
于是我们就正确求解出了这个图的最大流。
残留网络并不是简单地增加了一条运输流量的边，它是让之后寻找增广路时可以使水流沿着这条边流回去，也就是将之前的增广路“反悔”了。所以这种思想是正确的。为什么反边的容量为 $0$ 呢？因为只有再原边有流量的时候才能反悔，原边流量多少才能反悔多少。
## Dinic
Dinic算法就成功实现了这种思想。寻找增广路就是寻找一条可行解，而不是最优解，若使用 bfs 会几乎遍历整张图，而 dfs 就可以很快找到一条增广路。但是 dfs 也容易被卡，因为可能会绕远路甚至流回已经过的点，就会浪费时间。所以 Dinic 结合了 bfs 与 dfs，新增了分层图这个概念。
DInic算法会首先进行一次 bfs，按照搜索的先后顺序分层，构建分层图。然后进行 dfs，此时 dfs 有一个限制，就是流量只会从上一层流到下一层。这样可能已经找不到增广路了，但实际上还有许多增广路没被发现。这时就需要再一次进行 bfs，注意 bfs 不会经过容量为 $0$ 的边，这样就会构建一个新的分层图，再进行 dfs 。最后直到一次 bfs 无法到达汇点，即不存在增广路，Dinic 结束。此时的总流量就是最大流。
Dinic成功解决了之前dfs被卡的问题，因为流量只能从高的点向低的点流，并且bfs次数较少。不过Dinic还是要进行数次bfs，所以出现了更优的算法ISAP，可以只进行一次bfs。
## 优化
dfs过程中我们会一直流直到把一条边流完再转到下一条边，如果一条边有容量但是却没有增广路，说明这条边的下一个点已经没有更多容量了，我们就可以把连接的这个点的高度标记为 $0$ ，下一次就不会再搜索到这个点了。
不过 Dinic 还是很慢，甚至比 EK 还慢，这时候我们就需要拯救 Dinic 的当前弧优化。上面说到，dfs 过程中会一直流直到把一条边流完再转到下一条边，当搜索到一条边的时候说明这条边之前的所有边都已经没有增广路了，我们就可以直接将 $t$ 指向这条边，这样下一次就不会再次搜索已经没有流量的边了。
## 无向图
无向图的最大流可以通过建立两组边来实现，但是也可以更简单地去掉流量为 $0$ 的反向边，也就是对于一条容量为 $r$ 的无向边，从起点到终点建立一条容量为 $r$ 的无向边，再从终点到起点建立一条容量为 $r$ 的无向边即可。
## code
``` cpp
#include<cstdio>
#include<algorithm>
#include<queue>
using namespace std;
int n,m,p=1,s1,s2,t[10001],t0[10001],f[10001];
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
    for(int i=1;i<=n;++i) f[i]=0;
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
    scanf("%d%d%d%d",&n,&m,&s1,&s2);
    for(int i=1;i<=m;++i)
    {
        int x,y,r;
        scanf("%d%d%d",&x,&y,&r);
        road(x,y,r);
        road(y,x,0);
    }
    int r=0;
    while(bfs())
    {
        for(int i=1;i<=n;++i) t0[i]=t[i];
        r+=dfs(s1,1e9);
    }
    printf("%d",r);
    return 0;
}
```