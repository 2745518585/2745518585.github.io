---
title: '四边形不等式优化线性dp'
date: 2022-05-21 13:38:07
tags: [动态规划,四边形不等式优化dp]
description: ' '
---

## 前言

经典题：[P1912 [NOI2009] 诗人小G](https://www.luogu.com.cn/problem/P1912)
有一些奇怪的 dp 题目，它的转移方程有很多高次项，没办法单调队列优化或斜率优化， 但是我们仍然可以找到一些规律，比如说决策单调性。
我们举一个非常形象的例子，一个数列，$f[i]$ 表示前 ii 个数中的最大值，$g[i]$ 表示前 $i$ 个数中第一个出现的最大数的下标，那么很显然，$g$ 是单调不下降的，因为任意 $i,j(i<j)$ 都必然有 $f[i] \leqslant f[j]$，又有 $\forall k \in [1,j),a[k] < f[j] = a[g[j]]$，所以如果 $g[j]<g[i]$，必然有 $f[j] = a[g[j] < a[g[i]] = f[i]$，矛盾，故 $g[j] \leqslant g[i]$。如果我们非常傻地用 $f[i]=\max\limits_{1 \leqslant j \leqslant i}(a[i])$ 来转移，我们就可以将其优化为 $f[i]=\max\limits_{g[i-1] \leqslant j \leqslant i}(a[i])$。这就叫做决策单调性，其中 $g[i]$ 叫做 $i$ 位置的最优决策。

## 基本思路

如果一个转移方程存在决策单调性，我们就可以像上面那样优化了。但是显然还不够，我们可以换一种思路，原来我们是知道 $i$ 寻找 $g[i]$，现在我们在知道 $g[i]$ 的情况下去找 $i$，就相当于是在转移时知道 $j$ 去枚举 $i$。虽然看起来没有什么变化，但是别忘了还有决策单调性。假如我们现在知道了 $[1,n]$ 每个位置的最优决策（决策在 $[1,j]$ 范围内），由于决策单调性，如果位置 $i$ 的最优决策是 $j$，那么 $\forall i \in[j,n],g[i] \geqslant j$，由于是在 $[1,j]$ 中的最优决策，所以 $\forall i \in[j,n],g[i] = j$。所以我们一定能找到一个 $j$，使得 $\forall i \in[j,n],g[i] = j$ 且 $\forall i \in[1,j-1],g[i] < j$，我们只需要在序列中二分即可。

## 实现方法

但是二分之后我们还需要修改整个区间 $[j,n]$，很自然能想到线段树，但其实还有更好的做法：使用三元组。一个三元组 $(l,r,k)$ 表示在区间 $[l,r]$ 中所有位置的最优决策都是 $k$，然后用一个队列来存储所有三元组。首先插入三元组 $(1,n,0)$，之后的操作就很像单调队列了。
首先要找到 $i$ 的最优决策，只需要将队首右端点都比 $i$ 小的三元组出队，当前的队首三元组的最优决策 $k$ 就是 $i$ 的最优决策。然后我们要将 $i$ 作为一个决策插入序列，如果 $i$ 用来转移 $n$ 位置都没有当前最优决策 $g[n]$ 好，说明整个序列的最优决策都比 $i$ 小，跳过。否则，不断比较队尾元素 $(l,r,k)$ 与 $i$，如果用 $i$ 来转移 $l$ 比用 $k$ 来转移 $l$ 要好，就说明 $[l,r]$ 的最优决策都是 $i$，删除队尾。但是如果 $i$ 并不是整个 $[l,r]$ 的最优决策，就需要在 $[l,r+1]$ 中二分查找分界线 $t$（$t$ 的最优决策也为 $i$），把右端点改为$ t-1$，然后插入三元组 $(t,n,i)$。这里还要注意特判，如果队列中已经没有三元组了，直接插入 $(i+1,n,i)$。

## 证明

知道了如何解决满足决策单调性的问题。我们还要知道如何证明决策单调性。这就要引入四边形不等式：

$$\forall a \leqslant b \leqslant c \leqslant d,w(a,d)+w(b,c) \geqslant w(a,b)+w(c,d)$$

四边形不等式还有另一个表述方法：

$$\forall a<b,w(a,b+1)+w(a+1,b) \geqslant w(a,b)+w(a+1,b+1)$$

证明：

设 $a<c$，则有 $w(a,c+1)+w(a+1,c) \geqslant w(a,c)+w(a+1,c+1)$。

若 $a+1<c$，则有 $w(a+1,c+1)+w(a+2,c) \geqslant w(a+1,c)+w(a+2,c+1)$。

两式相加，消去相同项可得 $w(a,c+1)+w(a+2,c) \geqslant w(a,c)+w(a+2,c+1)$。

类似的，只要 $a+k<c$ 就可以得到 $w(a,c+1)+w(a+k,c) \geqslant w(a,c)+w(a+k,c+1)$。

所以对于 $a \leqslant b \leqslant c$，就有 $w(a,c+1)+w(b,c) \geqslant w(a,c)+w(b,c+1)$。

同理可证对于 $a \leqslant b \leqslant c \leqslant d$，有 $w(a,d)+w(b,c) \geqslant w(a,c)+w(b,d)$。

而对于证明决策单调性，有如下定理：

> 在状态转移方程 $f[i]=\min\limits_{0\leqslant j <i}\{f[j]+w(j,i)\}$ 中，若函数 $w$ 满足四边形不等式，则 $f$ 具有决策单调性。
证明：

$\forall i \in [1,n]$，$\forall j \in [0,g[i]−1]$，由 $g[i]$ 的最优性可得：

$$f[g[i]]+w(g[i],i)\leqslant f[j]+w(j,i)\tag1$$

设有 $i' \in [i+1,n]$，因为 $w$ 满足四边形不等式，所以

$$w(j,i')+w(g[i],i)\geqslant w(j,i)+w(g[i],i')$$

$$w(g[i],i')-w(g[i],i)\leqslant w(j,i')-w(j,i)\tag2$$

$(1)(2)$ 两式相加，可得：

$$f[g[i]]+w(g[i],i') \leqslant f[j]+w(j,i')$$

所以，对于 $i$ 以后的任意一个 $i′$，$g[i]$ 都是比任意 $j<g[i]$ 更优的决策，故 $f$ 具有决策单调性。

## code

下面是经典题 P1912 的代码。
```cpp
#include<cstdio>
#include<algorithm>
#include<cstring>
using namespace std;
typedef long long ll;
typedef long double ld;
const int N=100001;
int n,k,T,R,g[N];
ll m,a[N];
ld f[N];
char c[N][101];
bool h[N];
struct str
{
    int l,r,k;
}Q[N];
ld pow(ll x,int p)
{
    ld s=1;
    for(int i=1;i<=p;++i) s*=x;
    return s;
}
ld abc(int x,int y)
{
    return f[y]+pow(abs(a[x]-a[y]+x-y-1-m),k);
}
void dp()
{
    scanf("%d%lld%d",&n,&m,&k);
    for(int i=1;i<=n;++i)
    {
        scanf("%s",c[i]);
        a[i]=a[i-1]+strlen(c[i]);
    }
    T=0,R=-1;
    Q[++R]=(str){1,n,0};
    f[0]=0;
    for(int i=1;i<=n;++i)
    {
        while(T<=R&&Q[T].r<i) ++T;
        Q[T].l=i;
        f[i]=abc(i,Q[T].k);
        g[i]=Q[T].k;
        if(f[i]>1e18) continue;
        if(T<=R&&abc(n,i)>abc(n,Q[R].k)) continue;
        while(T<=R&&abc(Q[R].l,i)<=abc(Q[R].l,Q[R].k)) --R;
        if(T<=R)
        {
            int l=Q[R].l,r=Q[R].r+1;
            while(l<r)
            {
                int z=l+r>>1;
                if(abc(z,i)<=abc(z,Q[R].k)) r=z;
                else l=z+1;
            }
            Q[R].r=l-1;
            Q[++R]=(str){l,n,i};
        }
        else Q[++R]=(str){i+1,n,i};
    }
    if(f[n]>1e18)
    {
        printf("Too hard to arrange\n");
        return;
    }
    printf("%lld\n",(ll)f[n]);
    for(int i=1;i<=n;++i) h[i]=false;
    int x=n;
    while(x>=1)
    {
        h[x]=true;
        x=g[x];
    }
    for(int i=1;i<=n;++i)
    {
        printf("%s",c[i]);
        if(h[i]==true) printf("\n");
        else printf(" ");
    }
}
int main()
{
    int T;
    scanf("%d",&T);
    while(T--)
    {
        dp();
        printf("--------------------\n");
    }
    return 0;
}
```