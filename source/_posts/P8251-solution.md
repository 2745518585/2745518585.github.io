---
title: '[NOI Online 2022 提高组] 丹钓战'
date: 2022-03-26 13:15:28
tags: [可持续化线段树,主席树,NOIP]
description: ' '
---

[P8251 [NOI Online 2022 提高组] 丹钓战](https://www.luogu.com.cn/problem/P8251)
这道题我的方法是**主席树**，请不了解主席树的同学移步这道题： [P3834 【模板】可持久化线段树 2](https://www.luogu.com.cn/problem/P3834)
## 分析
### 模拟入栈
首先我们可以发现，这个栈的元素 $b_i$ 一定是单调下降的，如果要模拟入栈的操作，我们可以通过二分查找第一个比 $b_i$ 大的元素来模拟，可以在 $n\log n$ 的复杂度内完成一次模拟。但是同时还要满足任意两个相邻元素的 $a$ 不同，我们只需要在找到第一个比 $b_i$ 大的元素后，弹出栈顶直到栈顶元素的 $a$ 与 $a_i$ 不相等。注意要首先把 $inf$ 入栈，防止越界。
$b$ 代表 $b$ 序列，$c$ 代表栈。
```cpp
int l=0,r=t,z;
while(l<r)
 {
    z=(l+r+1)>>1;
    if(b[i]<b[c[z]]) l=z;
    else r=z-1;
}
while(a[c[l]]==a[i]) --l;
```
### 查询
如果我们先把所有元素按顺序入栈，由于任意的 $[l,r]$ 都应该是 $[1,n]$ 的一部分，并且栈中的元素编号一定递减，所以 $\forall [l,r]$，最终形成的栈一定是 $[1,r]$ 形成的栈的一部分，具体来说就是 $[1,r]$ 形成的栈去掉所有编号比 $l$ 小的元素。如果 $r$ 号元素想要在 $[l,r]$ 形成的栈中成为第一个，那么 $[1,r]$ 所形成的的栈中在 $r$ 之前的元素的编号必然都比 $l$ 小，只有这样前面所有元素才会被删去。所以用 $d$ 来储存每个数入栈后前一个元素的编号，那么 $[l,r]$ 中成功二元组的个数就是 $k \in [l,r],d_k < l$ 成立的 $k$ 的个数。
### 优化
但是上面这种方法的复杂度为 $O(n\times q)$ ，显然过不了。通过上面的分析，我们发现查询的实质是求 $d$ 中 $[l,r]$ 中小于 $l$ 的数的个数。有什么数据结构能实现？主席树！于是这道题就变成了主席树的板子题。然后，就没有然后了。
## code
```cpp
#include<cstdio>
#include<iostream>
#include<algorithm>
using namespace std;
int read()
{
    char c=getchar();int x=0;bool f=0;
    for(;!isdigit(c);c=getchar())f^=!(c^45);
    for(;isdigit(c);c=getchar())x=(x<<1)+(x<<3)+(c^48);
    if(f)x=-x;return x;
}
int n,m,p,t,a[500001],b[500001],c[500001],rt[500001];
struct tree
{
    int l,r,s;
}T[30000001];
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
int num(int x,int l,int r,int k)
{
    if(l==r) return 0;
    int z=l+r>>1;
    if(k==z) return T[T[x].l].s;
    if(k<z) return num(T[x].l,l,z,k);
    return T[T[x].l].s+num(T[x].r,z+1,r,k);
}
int main()
{
    freopen("stack.in","r",stdin);
    freopen("stack.out","w",stdout);
    n=read(),m=read();
    for(int i=1;i<=n;++i) a[i]=read();
    for(int i=1;i<=n;++i) b[i]=read();
    a[0]=0;
    b[0]=1e9;
    c[0]=0;
    build(rt[0],0,n);
    for(int i=1;i<=n;++i)
    {
        int l=0,r=t,z;
        while(l<r)
        {
            z=(l+r+1)>>1;
            if(b[i]<b[c[z]]) l=z;
            else r=z-1;
        }
        while(a[c[l]]==a[i]) --l;
        t=l+1;
        c[t]=i;
        rt[i]=rt[i-1];
        modify(rt[i],0,n,c[l]);
    }
    for(int i=1;i<=m;++i)
    {
        int l=read(),r=read();
        printf("%d\n",num(rt[r],0,n,l-1)-num(rt[l-1],0,n,l-1));
    }
    return 0;
}
```