---
title: 'Codeforces Round 775 A ~ D 题解'
date: 2023-03-24 10:46:40
tags: [CF]
description: 'Codeforces Round 633 (Div. 1) A ~ D 题解'
---

## A. Powered Addition

### 题意

给定一个数组 $a$，选定一个 $x$，给 $a_i$ 加上 $[0,2^x-1]$ 范围内的数，要使最后 $a$ 单调不降，求最小的 $x$。

$n \leq 10^5$

### 解法

考虑找到 $a_i-a_j(i<j)$ 的最大值，显然 $[i+1,n]$ 中的数都不比 $a_j$ 小，$[1,j-1]$ 的数也都不比 $a_i$ 大，需要加的数不超过 $a_j-a_i$，其他数也不会影响这对数，所以要加的最大的数就是 $a_i-a_j(i<j)$ 的最大值。

### code

```cpp
#include<cstdio>
#include<algorithm>
using namespace std;
const int N=1000001;
int n,a[N];
int main()
{
    int T;
    scanf("%d",&T);
    while(T--)
    {
        scanf("%d",&n);
        int w=-1e9,s=0;
        for(int i=1;i<=n;++i)
        {
            scanf("%d",&a[i]);
            s=max(s,w-a[i]);
            w=max(w,a[i]);
        }
        int t=0;
        while(s) ++t,s/=2;
        printf("%d\n",t);
    }
    return 0;
}
```

## B. Edge Weight Assignment

### 题意

有一棵树，需要给每条边赋正整数权值，使得任意两个叶子的路径上边权异或和为 $0$。

$3 \leq n \leq 10^5$

### 提示

对于任意一个点，到所有叶子的路径权值相等。

### 解法

先看最小值，我们尝试把所有边权值都赋成 $x$，可以发现不合法当且仅当有两个叶子之间有奇数条边。我们可以再加入两个权值 $y,z$ 满足 $y \oplus z = x$，这样三条边权值异或就可以等于 $0$ 了。因为 $n \geq 3$，所以不存在两个叶子之间只有一条边，任意奇数路径至少有三条边，即有奇数路径最小值为 $3$，否则为 $1$。

再看最大值。任意找一个非叶子点，我们要保证这个点到所有叶子路径权值相等，设为 $x$，如果路径上只有一条边，这条边权值就只能是 $x$；否则我们有无穷多种选择来得到 $x$，这些边和其他边的权值就能保证不同。即一个点到多个叶子路径上都只有一条边，这些边权值一定相同。

### code

```cpp
#include<cstdio>
#include<algorithm>
using namespace std;
const int N=1000001;
int n,m,p=1,t[N],l[N],f[N];
bool u,v;
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
void dfs(int x,int fa,int w)
{
    if(l[x]==1)
    {
        if(w%2==0) u=true;
        else v=true;
    }
    for(int i=t[x];i!=0;i=a[i].q)
    {
        if(a[i].m==fa) continue;
        dfs(a[i].m,x,w^1);
    }
}
int main()
{
    scanf("%d",&n);
    for(int i=1;i<=n-1;++i)
    {
        int x,y;
        scanf("%d%d",&x,&y);
        road(x,y);
        road(y,x);
        ++l[x],++l[y];
    }
    dfs(1,0,0);
    if(u&&v) printf("%d ",3);
    else printf("%d ",1);
    for(int i=1;i<=n;++i)
    {
        if(l[i]!=1) continue;
        for(int j=t[i];j!=0;j=a[j].q) ++f[a[j].m];
    }
    int s=0;
    for(int i=1;i<=n;++i)
    {
        if(f[i]>1) s+=f[i]-1;
    }
    printf("%d",n-s-1);
    return 0;
}
```

## C. Perfect Triples

### 题意

有一个序列 $s$，初始为空，你需要找到字典序最小的三元组 $(a,b,c)$ 满足 $a,b,c>0,a \oplus b \oplus c = 0$ 且 $a,b,c$ 没有在 $s$ 中出现过，将 $a,b,c$ 依次加入 $s$。$t$ 次询问，求 $s$ 中第 $n$ 个元素。

$1 \leq n \leq 10^{16},t \leq 10^5$

### 提示

可以打表找规律。

```
1 2 3
4 8 12
5 10 15
6 11 13
7 9 14
16 32 48
17 34 51
18 35 49
19 33 50
20 40 60
21 42 63
22 43 61
23 41 62
24 44 52
25 46 55
26 47 53
27 45 54
28 36 56
29 38 59
30 39 57
31 37 58
```

### 解法

我场上打表找的规律。

首先可以发现，大体可以分成 $1,[2,5],[6,21],\dots$，也就是第一组 $1$ 个，第二组 $4$ 个，第三组 $16$ 个。

第 $i$ 组中，$a$ 范围是 $[4^{i-1},2 \times 4^{i-1}-1]$，$b$ 范围是 $[2 \times 4^{i-1},3 \times 4^{i-1}-1]$，$c$ 范围是 $[3 \times 4^{i-1},4^i-1]$。$a$ 显然是递增的。观察 $b$，第二组中 $b$ 的第二个数 $9$ 被移到了最后，第三组中每四个数都是第二个数移到最后，同时第二个四个数也移到了整体的最后。然后不难发现 $c$ 就是把第四个数移到了第二个。

看了题解，可以发现这些数写成四进制后每一位都是 $(0,0,0),(1,2,3),(2,3,1),(3,1,2)$，因为 $1,2,3$ 两两异或等于另一个。

### code

```cpp
#include<cstdio>
#include<algorithm>
using namespace std;
typedef long long ll;
ll solve1(ll n)
{
    ll x=1;
    while(n>x) n-=x,x*=4;
    return x+n-1;
}
ll sum2(ll n,ll t)
{
    if(t==1) return 0;
    t/=4;
    if(n<=t) return sum2(n,t);
    else if(n<=t*2) return sum2(n-t,t)+t*2;
    else if(n<=t*3) return sum2(n-t*2,t)+t*3;
    else return sum2(n-t*3,t)+t;
}
ll solve2(ll n)
{
    ll x=1;
    while(n>x) n-=x,x*=4;
    return x*2+sum2(n,x);
}
ll sum3(ll n,ll t)
{
    if(t==1) return 0;
    t/=4;
    if(n<=t) return sum3(n,t);
    else if(n<=t*2) return sum3(n-t,t)+t*3;
    else if(n<=t*3) return sum3(n-t*2,t)+t;
    else return sum3(n-t*3,t)+t*2;
}
ll solve3(ll n)
{
    ll x=1;
    while(n>x) n-=x,x*=4;
    return x*3+sum3(n,x);
}
int main()
{
    int T;
    scanf("%d",&T);
    while(T--)
    {
        ll x;
        scanf("%lld",&x);
        if(x%3==1) printf("%lld\n",solve1((x+2)/3));
        else if(x%3==2) printf("%lld\n",solve2((x+2)/3));
        else if(x%3==0) printf("%lld\n",solve3((x+2)/3));
    }
    return 0;
}
```

## D. Nested Rubber Bands

### 题意

给定一棵树，把每个点变成平面上一个不自交的环，两个环相交当且仅当两个点之间存在边。如果一个环被完全包含在另一个环中，这个环就嵌套在另一个环中，求最多可以嵌套多少层。

### 提示

有边连接的两个环一定不能嵌套，同一个父亲的环可以嵌套在一起。

![1](/post-images/CF1338-solution-1.png?500x)

### 解法

如上图，我们可以发现，只有当前在最里面的环能够继续嵌套，一个红色环伸出来的环只有一个黑色环，其他只能作为蓝色环，无法延伸。红色环和黑色环就形成了一条链，而黑色环之间不能相邻，也不能和蓝色环相邻。也就是说，我们要在树上选一条链，在这条链和与这条链相邻的点中找最大独立集。

设 $f_{x,0}$ 为 $x$ 不在独立集中子树的方案，$f_{x,1}$ 为 $x$ 在独立集中子树的答案，这里 $x$ 为选出的链的一个端点。转移时注意只能选一个子树转移，所以是子树取 $\max$，然后以这个点为链的转折点更新答案，更新时注意处理其他儿子即可。详见代码。

```cpp
#include<cstdio>
#include<algorithm>
using namespace std;
const int N=1000001;
int n,m,s,p=1,t[N],f[N][2];
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
void dfs(int x,int fa)
{
    int w=0;
    for(int i=t[x];i!=0;i=a[i].q)
    {
        if(a[i].m==fa) continue;
        ++w;
    }
    for(int i=t[x];i!=0;i=a[i].q)
    {
        if(a[i].m==fa) continue;
        dfs(a[i].m,x);
        s=max(s,f[x][0]+max(f[a[i].m][0],f[a[i].m][1])+(w-2)+(fa!=0));
        s=max(s,f[x][1]+f[a[i].m][0]+1);
        f[x][0]=max(f[x][0],max(f[a[i].m][0],f[a[i].m][1]));
        f[x][1]=max(f[x][1],f[a[i].m][0]);
    }
    f[x][0]+=w-1;
    f[x][1]+=1;
}
int main()
{
    scanf("%d",&n);
    for(int i=1;i<=n-1;++i)
    {
        int x,y;
        scanf("%d%d",&x,&y);
        road(x,y);
        road(y,x);
    }
    dfs(1,0);
    printf("%d",s);
    return 0;
}
```

