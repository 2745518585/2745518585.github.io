---
title: '平衡树Splay'
date: 2022-03-15 13:23:25
tags: [平衡树]
description: ' '
---

## 前言
模板题：[P3369 【模板】普通平衡树](https://www.luogu.com.cn/problem/P3369)
我们已经学会了使用 Treap 来实现平衡树的功能，但是还有很多其他其它的平衡树，比如 Splay，Splay 不仅有平衡树功能，还可以用于其它地方，这是其它平衡树无法做到的。
## 基本思路
Splay 的核心是提根操作，也就是把一个节点提到根节点，每一次操作后都将当前节点提到根节点，这样 Splay 的形状就是在不断变化的，就可以较好地防止被卡。
Splay 的提根操作不止是讲一个节点提到根节点，它可以将一个节点提到它的某个祖先的儿子节点，这样在一些操作时直接将要操作的节点提到指定位置就可以很简单地解决问题。
## 储存与更新
```cpp
struct splay
{
    int k,a[2],f,s,t;
}T[10000001];
```
Splay 需要储存当前节点的元素值 $k$，左右儿子 $a$，父节点 $f$，当前节点子树的元素总数 $s$，当前节点的元素个数 $t$。
Splay 的更新方法与Treap相同。
```cpp
void pushup(int x)
{
    T[x].s=T[T[x].a[0]].s+T[T[x].a[1]].s+T[x].t;
}
```
## 提根操作
Splay 的提根操作同样也要用到 Treap 中的旋转操作，但是它不分左旋和右旋，旋转都是将当前节点旋转到它的父节点的位置。这样一直旋转，直到旋转到对应位置就停止即可。实际上我们会把当前节点旋转到目标节点的儿子节点，要判断当前节点的父节点是否为目标节点。
Splay 的旋转操作比较麻烦，因为我们需要在旋转的同时判断当前节点 $x$ 与其父节点 $y$、祖父节点 $z$ 的关系。用 $k$ 来储存当前节点 $x$ 是 $y$ 的左儿子还是右儿子，$0$ 表示左儿子，$1$ 表示右儿子。首先 $x$ 连接到 $z$ 的下面，替换 $y$ 的位置；接着把 $x$ 的儿子接到 $y$ 下面；最后把 $y$ 接在 $x$ 的下面。这里用了一个技巧：$k$^$1$ 其实就是 $y$ 除了 $k$ 的另一个儿子，当 $k$  为 $0$ 是，$k$^$1$ 为 $1$ 。
由于 $0$ 的儿子就是根节点 $rt$，所以如果目标是 $0$，当前节点就是根节点，需要在最后判断。
```cpp
void rotate(int x)
{
    int y=T[x].f,z=T[y].f,k=T[y].a[1]==x;
    T[z].a[T[z].a[1]==y]=x;
    T[x].f=z;
    T[y].a[k]=T[x].a[k^1];
    T[T[x].a[k^1]].f=y;
    T[x].a[k^1]=y;
    T[y].f=x;
    pushup(x);
    pushup(y);
}
void splay(int x,int k)
{
    while(T[x].f!=k) rotate(x);
    if(k==0) rt=x;
}
```
## 建树操作
不知道为什么，Splay 不加上下界就会 $\color{red}{WA}$，但是 Treap 就不会。我们需要在最开始时插入两个节点：正无穷和负无穷，来防止越界。根节点最好手动添加，另一个就用插入操作即可。
```cpp
void build()
{
    rt=q=1;
    T[1].f=0;
    T[1].s=T[1].t=1;
    T[1].k=1e9;
    add(rt,0,-1e9);
}
```
## 插入操作
Splay 的很多操作与 Treap 极其类似，包括插入操作，只需要在最后的时候进行提根改变Splay的形状即可。
```cpp
void add(int &x,int f,int k)
{
    if(x==0)
    {
        x=++q;
        T[x].k=k;
        T[x].t=T[x].s=1;
        T[x].f=f;
        splay(x,0);
        return;
    }
    if(T[x].k==k)
    {
        ++T[x].t;
        ++T[x].s;
        splay(x,0);
        return;
    }
    else if(k<T[x].k) add(T[x].a[0],x,k);
    else add(T[x].a[1],x,k);
}
```
## 删除操作
利用 Splay 可以改变形状的特点，就可以用独特的方法来删除节点。如果通过提根来使要删除的节点成为叶子结点，就可以直接删除这个点。我们可以先找到这个节点 $x$ 的前驱 $l$ 与后继 $r$，$l$ 和 $r$ 之间只有一个节点 $x$。我们先把 $l$ 提到根节点，再将 $r$ 提到 $l$ 的儿子节点。由于 $T[r].k>T[l].k$ ，所以 $r$ 一定是 $l$ 的右子节点。而 $r$ 的左子树，也就是比 $l$ 大而比 $r$ 小的部分就只有一个节点 $x$，直接删除或元素个数减一即可。
```cpp
void remove(int k)
{
    int l=find_f(rt,rt,k),r=find_b(rt,rt,k);
    splay(l,0);
    splay(r,l);
    --T[T[r].a[0]].t;
    --T[T[r].a[0]].s;
    if(T[T[r].a[0]].t==0)
    {
        T[T[r].a[0]].k=0;
        T[r].a[0]=0;
    }
    splay(r,0);
}
```
## 查询排名
在 Splay 中，查询一个元素的排名十分简单，就只需要将这个元素 $x$ 的前驱 $l$ 提到根节点，那么根节点与左子树中的元素一定比这个元素小。而且不存在一个元素比 $l$ 大而比 $x$ 小，也就是 $x$ 没有左儿子，说明比 $x$ 小的元素全部都在根节点与左子树中。所以 $x$ 的排名就是左子树的大小加上根节点的元素个数。
```cpp
int num(int k)
{
    splay(find_f(rt,rt,k),0);
    return T[T[rt].a[0]].s+T[rt].t;
}
```
## 查询对应排名的元素
Splay 的这个操作和 Treap 相同，只需要找到这个节点后将其提根即可。
```cpp
int sum(int x,int k)
{
    if(x==0) return 0;
    if(k<T[T[x].a[0]].s+1) return sum(T[x].a[0],k);
    if(k<=T[T[x].a[0]].s+T[x].t)
    {
        splay(x,0);
        return x;
    }
    return sum(T[x].a[1],k-T[T[x].a[0]].s-T[x].t);
}
```
## 查询前驱和后继
Splay 查询前驱后后继的操作也和 Treap 相同，只需要记录当前节点 $x$ 的根节点 $f$，并在找到了不存在的节点，也就是 $x=0$，那么就将这个点的上一个点也就是 $f$ 提根。对于后继的操作也一模一样。
由于 Splay 很多操作都要用到前驱和后继，所以这里的前驱和后继返回的都是节点编号，而不是元素的值。
```cpp
int find_f(int x,int f,int k)
{
    if(x==0)
    {
        splay(f,0);
        return 0;
    }
    if(k<=T[x].k) return find_f(T[x].a[0],x,k);
    int p=find_f(T[x].a[1],x,k);
    if(p==0) return x;
    return p;
}
int find_b(int x,int f,int k)
{
    if(x==0)
    {
        splay(f,0);
        return 0;
    }
    if(k>=T[x].k) return find_b(T[x].a[1],x,k);
    int p=find_b(T[x].a[0],x,k);
    if(p==0) return x;
    return p;
}
```
## Splay的调用
Splay 的这些操作调用方法与 Treap 一样，只有前驱、后继和相应排名的元素是返回的节点编号，需要转换为元素值。
```cpp
int main()
{
    build();
    scanf("%d",&m);
    for(int i=1;i<=m;++i)
    {
        int z,k;
        scanf("%d%d",&z,&k);
        if(z==1) add(rt,0,k);
        else if(z==2) remove(k);
        else if(z==3) printf("%d\n",num(k));
        else if(z==4) printf("%d\n",T[sum(rt,k+1)].k);
        else if(z==5) printf("%d\n",T[find_f(rt,rt,k)].k);
        else if(z==6) printf("%d\n",T[find_b(rt,rt,k)].k);
    }
    return 0;
}
```
## code
```cpp
#include<cstdio>
using namespace std;
int n,m,rt=0,q=0;
struct splay
{
    int k,a[2],f,s,t;
}T[10000001];
void pushup(int x)
{
    T[x].s=T[T[x].a[0]].s+T[T[x].a[1]].s+T[x].t;
}
void rotate(int x)
{
    int y=T[x].f,z=T[y].f,k=T[y].a[1]==x;
    T[z].a[T[z].a[1]==y]=x;
    T[x].f=z;
    T[y].a[k]=T[x].a[k^1];
    T[T[x].a[k^1]].f=y;
    T[x].a[k^1]=y;
    T[y].f=x;
    pushup(x);
    pushup(y);
}
void splay(int x,int k)
{
    while(T[x].f!=k) rotate(x);
    if(k==0) rt=x;
}
void add(int &x,int f,int k)
{
    if(x==0)
    {
        x=++q;
        T[x].k=k;
        T[x].t=T[x].s=1;
        T[x].f=f;
        splay(x,0);
        return;
    }
    if(T[x].k==k)
    {
        ++T[x].t;
        ++T[x].s;
        splay(x,0);
        return;
    }
    else if(k<T[x].k) add(T[x].a[0],x,k);
    else add(T[x].a[1],x,k);
}
void build()
{
    rt=q=1;
    T[1].f=0;
    T[1].s=T[1].t=1;
    T[1].k=1e9;
    add(rt,0,-1e9);
}
int find_f(int x,int f,int k)
{
    if(x==0)
    {
        splay(f,0);
        return 0;
    }
    if(k<=T[x].k) return find_f(T[x].a[0],x,k);
    int p=find_f(T[x].a[1],x,k);
    if(p==0) return x;
    return p;
}
int find_b(int x,int f,int k)
{
    if(x==0)
    {
        splay(f,0);
        return 0;
    }
    if(k>=T[x].k) return find_b(T[x].a[1],x,k);
    int p=find_b(T[x].a[0],x,k);
    if(p==0) return x;
    return p;
}
void remove(int k)
{
    int l=find_f(rt,rt,k),r=find_b(rt,rt,k);
    splay(l,0);
    splay(r,l);
    --T[T[r].a[0]].t;
    --T[T[r].a[0]].s;
    if(T[T[r].a[0]].t==0)
    {
        T[T[r].a[0]].k=0;
        T[r].a[0]=0;
    }
    splay(r,0);
}
int sum(int x,int k)
{
    if(x==0) return 0;
    if(k<T[T[x].a[0]].s+1) return sum(T[x].a[0],k);
    if(k<=T[T[x].a[0]].s+T[x].t)
    {
        splay(x,0);
        return x;
    }
    return sum(T[x].a[1],k-T[T[x].a[0]].s-T[x].t);
}
int num(int k)
{
    splay(find_f(rt,rt,k),0);
    return T[T[rt].a[0]].s+T[rt].t;
}
int main()
{
    build();
    scanf("%d",&m);
    for(int i=1;i<=m;++i)
    {
        int z,k;
        scanf("%d%d",&z,&k);
        if(z==1) add(rt,0,k);
        else if(z==2) remove(k);
        else if(z==3) printf("%d\n",num(k));
        else if(z==4) printf("%d\n",T[sum(rt,k+1)].k);
        else if(z==5) printf("%d\n",T[find_f(rt,rt,k)].k);
        else if(z==6) printf("%d\n",T[find_b(rt,rt,k)].k);
    }
    return 0;
}
```