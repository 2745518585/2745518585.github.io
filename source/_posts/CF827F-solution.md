---
title: 'CF827F 题解'
date: 2023-02-26 18:26:04
tags: [CF]
description: ' '
---

## 题意

一张无向图，边权为 $1$，第 $i$ 条边仅能在 $[l_i,r_i]$ 的时间通过，求 $1$ 到 $n$ 的最短路。

## 提示

注意这个是无向图，所以只要在这条边的时间范围内，就可以反复走同一条边。

## 思路

注意，接下来定义第 $i$ 条边可以走的时间范围是 $[l_i,r_i]$，也就是 $r_i$ 等于题目中的 $r_i-1$。

首先这是一张无向图，我们考虑如果在到达一个点但是还要等一会才能走出去时，我们可以反复走一条边来拖时间。由于需要 $2$ 的时间往返一次，所以需要把时间分奇偶讨论。也就是说，如果我们能在 $t \in [l_i,r_i]$ 的时间到达边 $i$ 的起点，那么我们可以在 $[t+1,r_i+1]$ 中奇偶性相同的任意时间到达终点。

我们可以考虑每一个点拆成奇数点和偶数点，然后对于一条边，一个点的奇数点向另一个点的偶数点连边，偶数点向奇数点连边，并调整时间边界的奇偶性。接下来的讨论已经没有奇偶限制。

假如从边 $i$ 到达了 $x$，接下来要走边 $j$，如果 $i$ 已经无法走了 $j$ 还没到时间，我们可以考虑反复走 $x$ 连出去的其他边 $k$。这种情况其实不用单独考虑，因为我们可以把从 $i$ 到 $j$（重复走 $i$ 和 $k$）转换为先从 $i$ 到 $k$（重复走 $k$），然后再从 $k$ 走到 $j$（重复走 $k$）。

我们用一个三元组 $(x,l,r)$ 表示我们可以在 $[l,r]$ 的时间内都可以从点 $x$ 出发，枚举出边 $i$。如果 $l_i>r$ 显然没法走。显然到达终点的时间最早是 $l+1$ 和 $l_i+1$ 取最大值，最晚是 $r_i+1$，因为我们可以重复走这条边。然后我们用一个类似 $\text{Dijkstra}$ 的东西维护：每一次取所有三元组中 $l$ 最小的，更新答案，一个点可以重复更新。但是每次遍历边显然要寄，我们可以发现每一次更新的 $l$ 是不降的，所以能更新的边的左端点也是不降的，我们只需要将边排序，每一次从上一次结束的地方开始。

## code

{% contentbox type:success title:code %}
```cpp
#include<cstdio>
#include<algorithm>
#include<vector>
#include<queue>
using namespace std;
const int N=1000001;
int n,m,t[N];
struct str
{
    int x,l,r;
    str(){}
    str(int x,int l,int r):x(x),l(l),r(r){}
    friend bool operator <(str a,str b)
    {
        return a.l>b.l;
    }
};
vector<str> a[N];
bool cmp(str a,str b)
{
    return a.l<b.l;
}
int main()
{
    scanf("%d%d",&n,&m);
    for(int i=1;i<=m;++i)
    {
        int x,y,l,r;
        scanf("%d%d%d%d",&x,&y,&l,&r);
        --r;
        int l1=l,l2=l,r1=r,r2=r;
        if(l%2==1) ++l1;
        else ++l2;
        if(r%2==1) --r1;
        else --r2;
        if(l1<=r1)
        {
            a[x].push_back(str(y+n,l1,r1));
            a[y].push_back(str(x+n,l1,r1));
        }
        if(l2<=r2)
        {
            a[x+n].push_back(str(y,l2,r2));
            a[y+n].push_back(str(x,l2,r2));
        }
    }
    for(int i=1;i<=n*2;++i)
    {
        sort(a[i].begin(),a[i].end(),cmp);
    }
    priority_queue<str> Q;
    Q.push(str(1,0,0));
    while(!Q.empty())
    {
        str k=Q.top();
        Q.pop();
        if(k.x==n||k.x==n*2)
        {
            printf("%d",k.l);
            return 0;
        }
        for(int i=t[k.x];i<a[k.x].size();++i)
        {
            if(a[k.x][i].l>k.r) break;
            t[k.x]=i+1;
            if(k.l<=a[k.x][i].r) Q.push(str(a[k.x][i].x,max(k.l,a[k.x][i].l)+1,a[k.x][i].r+1));
        }
    }
    printf("-1");
    return 0;
}
```
{% endcontentbox %}
