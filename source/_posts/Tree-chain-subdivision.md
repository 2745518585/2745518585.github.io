---
title: '树链剖分'
date: 2022-05-07 20:11:24
tags: [树,树链剖分]
description: ' '
---

## 前言

树链剖分是用来解决树上问题的经典方法，而且常数极小。这其中的一个经典问题就是 [最近公共祖先（LCA）](https://www.luogu.com.cn/problem/P3379)，这道题可以当做树剖的模板题。

## 基本思路

树链剖分，顾名思义，是把树剖分成很多条链。为什么要剖分成链？因为在解决树上问题时，一条链往往可以一次性操作，就如模板题 LCA，我们在两个点向上跳时就可以直接跳到所在链的链头。如何剖分？剖分有多种方法，其中重链剖分和长链剖分较为常见。重链剖分就是将这个节点与其子节点的子树中节点个数最多的子节点（又称“重儿子”）与这个节点连在一起组成一条链，其余子节点各自成一条链；长链剖分就是将这个节点与其子节点的子树深度最深的子节点连在一起组成一条链，其与子节点各自成一条链。

![1](/post-images/Tree-chain-subdivision-1.png?300x)

比如这样一棵树，对它进行重链剖分后就是这个样子：

![2](/post-images/Tree-chain-subdivision-2.png?500x)

而长链剖分后就是这个样子：

![3](/post-images/Tree-chain-subdivision-3.png?500x)

## 实现

树剖理解起来很容易，但是不容易实现。进行重链剖分对于每个节点，我们要处理一下信息：父亲，子树大小、深度、所在链头与重儿子。我们要进行两次 dfs，第一次处理基本信息：父亲、子树大小、深度与重儿子，第二次进行剖分，首先访问重儿子（顺序不能换，以后会用到），将当前节点的链头传递给重儿子；然后访问其余子节点，链头为子节点本身。这样我们就成功剖分了这棵树。
对于长链剖分，只需要将子树大小改为最大深度即可。

## LCA

要实现 LCA，我们需要先进行重链剖分，因为子树大小越多求这里面的点的可能越大。要找到点 $x$ 与点 $y$ 的 LCA，首先判断两个点有没有在同一条链中，也就是两个点的链头是否相同。如果不相同，将深度较深的点跳转至其链头的父节点，再进行判断；如果在，那么深度较浅的那一个点就是 LCA。证明也非常简单，如果不在同一条链中，深度较深的点所在的链头一定不是另一个点的祖先，所以 LCA 也一定不在这条链中，就跳到上一条链。

## code

```cpp
#include<cstdio>
#include<algorithm>
using namespace std;
const int N=500001;
int n,m,r,p=1,t[N];
struct tree
{
    int f,s,d,t,z;
}T[N];
struct road
{
    int m,q;
}a[N<<1];
void road(int x,int y)
{
    a[++p].m=y;
    a[p].q=t[x];
    t[x]=p;
}
void dfs1(int x)
{
    T[x].s=1;
    T[x].d=T[T[x].f].d+1;
    for(int i=t[x];i!=0;i=a[i].q)
    {
        if(a[i].m==T[x].f) continue;
        T[a[i].m].f=x;
        dfs1(a[i].m);
        T[x].s+=T[a[i].m].s;
        if(T[a[i].m].s>T[T[x].z].s) T[x].z=a[i].m;
    }
}
void dfs2(int x,int k)
{
    T[x].t=k;
    if(T[x].z!=0) dfs2(T[x].z,k);
    for(int i=t[x];i!=0;i=a[i].q)
    {
        if(a[i].m==T[x].f||a[i].m==T[x].z) continue;
        else dfs2(a[i].m,a[i].m);
    }
}
int LCA(int x,int y)
{
    while(T[x].t!=T[y].t)
    {
        if(T[T[x].t].d>=T[T[y].t].d) x=T[T[x].t].f;
        else y=T[T[y].t].f;
    }
    if(T[x].d<T[y].d) return x;
    else return y;
}
int main()
{
    scanf("%d%d%d",&n,&m,&r);
    for(int i=1;i<=n-1;++i)
    {
        int x,y;
        scanf("%d%d",&x,&y);
        road(x,y);
        road(y,x);
    }
    dfs1(r);
    dfs2(r,r);
    for(int i=1;i<=m;++i)
    {
        int x,y;
        scanf("%d%d",&x,&y);
        printf("%d\n",LCA(x,y));
    }
    return 0;
}
```