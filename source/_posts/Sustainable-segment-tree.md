---
title: '可持续化线段树'
date: 2022-03-10 13:40:48
tags: [可持续化线段树,主席树]
description: ' '
---

# 可持续化线段树
## 前言
&#8195； 模板题：[P3919 【模板】可持久化线段树 1（可持久化数组）](https://www.luogu.com.cn/problem/P3919)
我们学习了很多的数据结构，它们支持修改、查询的操作。但是如果我们想在过去的某个版本上进行操作，我们就需要可持续化数据结构，比如说可持续化线段树，它支持在 $O(\log n)$ 的时间内进行操作。
## 基本思想
朴素的方法实现可持续化需要记录每个版本，修改一次就需要 $O(n)$ 的时间来复制，不能接受。但是我们可以发现，线段树上每一次操作都只改变树上的一条链，如果我们只复制这条链，就只需要 $O(\log n)$ 的时间来复制了。
我们要在只复制一条链的情况下保持原树和新树都是完整的，就需要把没有改变的所有点与新的链连在一起，并记录每一个版本的根节点。由于每一次修改都要改变根节点，所以每一个版本都对应一个根节点，这个根节点的子树就是一颗完整的线段树。如下图：
![0](/post-images/Sustainable-segment-tree-1.jpg =450x)
![1](/post-images/Sustainable-segment-tree-2.jpg =450x)
（图片来自网络）
## 储存及更新
可持续化线段树不再像线段树那样储存左端点与右端点，而是左儿子与右儿子，因为可持续化线段树不再满足完全二叉树那样的编号规律，但是左右端点都可以轻松算出来。
```cpp
struct tree
{
    int l,r,s;
}T[100000001];
void pushup(int x)
{
    T[x].s=T[T[x].l].s+T[T[x].r].s;
}
```
## 建树
可持续化线段树的建立和线段树几乎一样，只是新节点的编号要单独算，这个过程要传址，就像平衡树那样。
```cpp
void build(int &x,int l,int r)
{
    x=++p;
    if(l==r)
    {
        T[x].s=a[l];
        return;
    }
    int z=l+r>>1;
    build(T[x].l,l,z);
    build(T[x].r,z+1,r);
}
```
## 修改
可持续化线段树的单点修改需要把所修改节点到根节点的所有节点全部备份一遍、更改，注意这个过程还要传递当前区间的左右端点，同时也要传址。
```cpp
void modify(int &x,int l,int r,int q,int k)
{
    T[++p]=T[x];
    T[p].s=k;
    x=p;
    if(l==r) return;
    int z=l+r>>1;
    if(q<=z) modify(T[x].l,l,z,q,k);
    else modify(T[x].r,z+1,r,q,k);
}
```
## 查询
查询操作就和线段树十分相似了，只是同时需要传递左右端点。
```cpp
int sum(int x,int l,int r,int l0,int r0)
{
    if(l>=l0&&r<=r0) return T[x].s;
    int z=l+r>>1,s=0;
    if(l0<=z) s+=sum(T[x].l,l,z,l0,r0);
    if(r0>z) s+=sum(T[x].r,z+1,r,l0,r0);
    return s;
}
```
## 调用
可持续化线段树调用的根节点是指定版本的根节点复制过来的根节点，这样调用的时候就会在这个根节点下面新建节点，同时这个根节点也会在操作后指向新的节点。不过板子题比较特殊的是查询操作也要新建版本，我们只需要把根节点复制一遍即可，其它节点都不用变。可持续化线段树一般也要建树，对应的根节点为 $0$，这样可以防止在一些操作中越界。
```cpp
build(rt[0],1,n);
for(int i=1;i<=m;++i)
{
    int d,z,x,y;
    scanf("%d%d%d%d",&d,&z,&x,&y);
    rt[i]=rt[d];
    if(z==1)
    {
         modify(rt[i],1,n,x,y);
    }
    else
    {
        printf("%d\n",sum(rt[i],1,n,x,y));
    }
}
```
## code
```cpp
#include<cstdio>
#include<algorithm>
using namespace std;
int n,m,p=0,a[1000001],b[1000001],rt[1000001];
struct tree
{
    int l,r,s;
}T[100000001];
void pushup(int x)
{
    T[x].s=T[T[x].l].s+T[T[x].r].s;
}
void build(int &x,int l,int r)
{
    x=++p;
    if(l==r)
    {
        T[x].s=a[l];
        return;
    }
    int z=l+r>>1;
    build(T[x].l,l,z);
    build(T[x].r,z+1,r);
}
void modify(int &x,int l,int r,int q,int k)
{
    T[++p]=T[x];
    T[p].s=k;
    x=p;
    if(l==r) return;
    int z=l+r>>1;
    if(q<=z) modify(T[x].l,l,z,q,k);
    else modify(T[x].r,z+1,r,q,k);
}
int sum(int x,int l,int r,int l0,int r0)
{
    if(l>=l0&&r<=r0) return T[x].s;
    int z=l+r>>1,s=0;
    if(l0<=z) s+=sum(T[x].l,l,z,l0,r0);
    if(r0>z) s+=sum(T[x].r,z+1,r,l0,r0);
    return s;
}
int main()
{
    scanf("%d%d",&n,&m);
    for(int i=1;i<=n;++i)
    {
        scanf("%d",&a[i]);
    }
    build(rt[0],1,n);
    for(int i=1;i<=m;++i)
    {
        int d,z,x,y;
        scanf("%d%d%d%d",&d,&z,&x,&y);
        rt[i]=rt[d];
        if(z==1)
        {
            modify(rt[i],1,n,x,y);
        }
        else
        {
            printf("%d\n",sum(rt[i],1,n,x,y));
        }
    }
    return 0;
}
```
# 主席树
## 前言
&#8195； 模板题：[P3834 【模板】可持久化线段树 2](https://www.luogu.com.cn/problem/P3834)
主席树是可持续化线段树的一种经典应用，但是它不是用来解决需要操作历史版本的问题的，而是用来解决一个不可修改的序列的，经典问题就是一个数在区间 $[l,r]$ 的排名和区间第 $k$ 小，不带修改操作的话主席树就是最好的解法。
## 基本思想
要找一个数在区间 $[l,r]$ 的排名，我们很容易想到对于每个数做前缀和的方法，但是要从第 $i-1$ 个数转移到第 $i$ 个数，就需要转移 $q$ 次（$q$ 是所有数离散化后的数的个数），不能接受。但是我们发现，每一次转移都只有一个数发生了改变，其它的都与上一个版本相同。而可持续化线段树就是在历史版本上用 $\log n$ 的时间复制并修改一个版本上的一个点。
主席树的基本思想是把 $1\sim n$ 这 $n$ 个点看做 $n$ 个版本，对于版本 $i$ 记录每个数 $1\sim q$ 在 $[1,i]$ 中出现的次数 $b$，而主席树维护的就是 $b$ 数组。为什么要这样做？如果我们要找离散化后的数 $x$ 在区间 [l,r] 中的排名，我们只需要在版本 $l-1$ 中找到 $[1,x-1]$ 的和，同时在 $r$ 中找到 $[1,r]$ 的和，相减即为答案。而主席树版本 $i$ 与版本 $i-1$ 之间只有原序列 $a$ 的第 $i$ 上的数对应的位置的值改变了，相当于单点修改。但是在查询的时候有可能要访问到版本 $0$ 所以我们要先建立 $0$ 号版本，防止越界。
要解决区间第 $k$ 小的问题，我们只需要在线段树上进行二分。同时维护版本 $l-1$ 的当前节点 $x1$ 与版本 $r$ 上的当前节点 $x2$，如果 $x2$ 的左子树的和减去 $x1$ 左子树的和小于等于 $k$，表明区间 $[l,r]$ 中 $1\sim a_q$ 的数的总数比 $k$ 多，答案在左子树上，否则在右子树上。
## code
```cpp
#include<cstdio>
#include<algorithm>
using namespace std;
int n,m,p=0,a[1000001],b[1000001],rt[1000001];
struct tree
{
    int l,r,s;
}T[10000001];
void pushup(int x)
{
    T[x].s=T[T[x].l].s+T[T[x].r].s;
}
void build(int &x,int l,int r)
{
    x=++p;
    if(l==r) return;
    int z=l+r>>1;
    build(T[x].l,l,z);
    build(T[x].r,z+1,r);
}
void modify(int &x,int l,int r,int q)
{
    T[++p]=T[x];
    ++T[p].s;
    x=p;
    if(l==r) return;
    int z=l+r>>1;
    if(q<=z) modify(T[x].l,l,z,q);
    else modify(T[x].r,z+1,r,q);
}
int sum(int x1,int x2,int l,int r,int q)
{
    if(l==r) return b[l];
    int z=l+r>>1,k=T[T[x2].l].s-T[T[x1].l].s;
    if(q<=k) return sum(T[x1].l,T[x2].l,l,z,q);
    else return sum(T[x1].r,T[x2].r,z+1,r,q-k);
}
int main()
{
    scanf("%d%d",&n,&m);
    for(int i=1;i<=n;++i)
    {
        scanf("%d",&a[i]);
        b[i]=a[i];
    }
    sort(b+1,b+n+1);
    int q=unique(b+1,b+n+1)-b-1;
    build(rt[0],1,q);
    for(int i=1;i<=n;++i)
    {
        rt[i]=rt[i-1];
        modify(rt[i],1,q,lower_bound(b+1,b+q+1,a[i])-b);
    }
    for(int i=1;i<=m;++i)
    {
        int l,r,k;
        scanf("%d%d%d",&l,&r,&k);
        printf("%d\n",sum(rt[l-1],rt[r],1,q,k));
    }
    return 0;
}
```