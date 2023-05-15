---
title: AtCoder Regular Contest 160 题解
date: 2023-05-15 20:45:11
tags: [AT]
description: ' '
---

## A - Reverse and Count

### 题意

给定一个长为 $n$ 的数列，选择一个区间并翻转，求得到字典序第 $k$ 小的序列。

$n \leq 7000,k \leq \frac{n(n+1)}{2}$

### 解法

对于一个子问题 $(m,l)$ 考虑第 $1$ 位填什么：$i$ 从 $1$ 到 $m$ 枚举，如果 $i \not = a_1$，必须把数 $i$ 翻转到第 $1$ 位，只有一种可能。否则数 $i$ 已经在第 $1$ 位，可以在 $[2,m]$ 中旋转，共 $\frac{m(m-1)}{2}$ 种，也可以不改变数组，进行大小为 $1$ 的旋转，在 $[2,m]$ 中进行大小为 $1$ 的翻转已经被统计了，所以总情况数就是 $\frac{m(m-1)}{2}+(n-m+1)$。如果 $k$ 大于当前情况数，那么就从 $k$ 中减去情况数并考虑 $i+1$ 的情况，否则遇到子问题就递归，只有一种情况就返回答案。

### code

```cpp
#include<cstdio>
#include<algorithm>
#include<vector>
using namespace std;
const int N=1000001;
int n,k;
vector<int> solve(int m,int k,vector<int> a)
{
    for(int i=1;i<=m;++i)
    {
        if(a[1]==i)
        {
            if(k<=m*(m-1)/2+(n-m+1))
            {
                vector<int> b;
                b.resize(m+1);
                for(int j=1;j<=m-1;++j) b[j]=a[j+1]-(a[j+1]>i);
                vector<int> c=solve(m-1,k,b),d;
                d.resize(m+1);
                for(int j=1;j<=m-1;++j) d[j+1]=c[j]+(c[j]>=i);
                d[1]=i;
                return d;
            }
            else k-=m*(m-1)/2+(n-m+1);
        }
        else
        {
            if(k==1)
            {
                int x=0;
                for(int j=1;j<=m;++j)
                {
                    if(a[j]==i)
                    {
                        x=j;
                        break;
                    }
                }
                for(int j=1;j<=x/2;++j) swap(a[j],a[x-j+1]);
                return a;
            }
            else --k;
        }
    }
}
int main()
{
    scanf("%d%d",&n,&k);
    vector<int> a;
    a.resize(n+1);
    for(int i=1;i<=n;++i)
    {
        scanf("%d",&a[i]);
    }
    vector<int> b=solve(n,k,a);
    for(int i=1;i<=n;++i)
    {
        printf("%d ",b[i]);
    }
    return 0;
}
```

## B - Triple Pair

### 题意

求有多少对正整数 $(x,y,z)$ 满足 $xy,yz,zx \leq n$，对 $998244353$ 取模。

$n \leq 10^9$

### 解法

考虑整除分块，先枚举 $x$，显然对于 $\frac{n}{x}$ 相等的所有 $x$，$y,z$ 的个数相等，设 $f_i$ 为 $y,z \leq i,yz \leq n$ 的个数，知道了 $f$ 就可以快速求答案。

枚举 $i$，对 $i$ 整除分块，$f_i$ 等于上一块也就是 $f_{\frac{n}{\frac{n}{i-1}}}$ 加上 $y,z$ 其中一个等于 $i$ 的情况，注意减去 $y,z$ 都等于 $i$ 的情况和特判 $i=1$。复杂度 $O(\sqrt{n})$。

### code

```cpp
#include<cstdio>
#include<algorithm>
#include<cmath>
using namespace std;
typedef long long ll;
const int N=1000001;
const ll P=998244353;
ll n,f1[N],f2[N];
ll &sum(ll x)
{
    if(x*x<=n) return f1[n/(n/x)];
    return f2[n/x];
}
int main()
{
    int T;
    scanf("%d",&T);
    while(T--)
    {
        scanf("%lld",&n);
        ll p=sqrt(n)+100;
        for(ll i=0;i<=p;++i) f1[i]=f2[i]=0;
        for(ll i=1;i<=n;++i)
        {
            ll j=n/(n/i);
            if(i>1) sum(j)=sum(n/(n/(i-1)));
            else sum(j)=0;
            ll z=min(n/i,j);
            sum(j)=(sum(j)+(j-i+1)*z*2%P-max(z-i+1,0ll)+P)%P;
            i=j;
        }
        ll s=0;
        for(ll i=1;i<=n;++i)
        {
            ll j=n/(n/i);
            s=(s+(j-i+1)*sum(n/i)%P)%P;
            i=j;
        }
        printf("%lld\n",s);
    }
    return 0;
}
```

## C - Power Up

### 题意

给定一个长为 $n$ 的可重集 $a$，可以重复进行以下操作：

- 选择两个相同的数 $x$ 从可重集中删除这两个数并加入 $x+1$。

求可能得到的不同的可重集个数，模 $998244353$。

$n \leq 2 \times 10^5, a_i \leq 2 \times 10^5$

### 解法

设 f_{i,j} 表示 $[1,i]$ 的数有 $j$ 个的方案数，g_{i,j} 表示 $[1,i]$ 的数大于等于 $j$ 个的方案数，$b_i$ 表示初始可重集中有 $b_i$ 个 $i$。显然有转移：$f_{i,j+b_i} = g_{i-1,j*2}$。时间复杂度 $n^2$

考虑优化，显然一个数 $i$ 对 $i$ 的个数贡献为 $1$，$i+1$ 最多为 $\frac{1}{2}$，$i+2$ 最多为 $\frac{1}{4}$，也就是一个数对所有值的个数贡献和为 $O(1)$ 的，我们只需要对于每个值找到最多有多少个数即可。用优先队列维护堆，模拟一遍即可。

### code

```cpp
#include<cstdio>
#include<algorithm>
#include<vector>
#include<queue>
using namespace std;
typedef long long ll;
const int N=1000001;
const ll P=998244353;
int n,q,b[N],d[N];
ll *f[N];
int main()
{
    scanf("%d",&n);
    priority_queue<int,vector<int>,greater<int>> Q;
    for(int i=1;i<=n;++i)
    {
        int x;
        scanf("%d",&x);
        q=max(q,x);
        Q.push(x);
        ++b[x],++d[x];
    }
    while(!Q.empty())
    {
        int k1=Q.top();
        Q.pop();
        if(Q.empty()||k1!=Q.top()) continue;
        int k2=Q.top();
        Q.pop();
        Q.push(k1+1);
        ++d[k1+1];
    }
    f[0]=new ll[10];
    f[0][0]=1;
    for(int i=1;i<=q*2;++i)
    {
        f[i]=new ll[d[i]+10];
        for(int j=0;j<=b[i]-1;++j) f[i][j]=0;
        for(int j=b[i];j<=d[i];++j)
        {
            if((j-b[i])*2<=d[i-1]) f[i][j]=f[i-1][(j-b[i])*2];
        }
        for(int j=d[i]-1;j>=0;--j) f[i][j]=(f[i][j]+f[i][j+1])%P;
    }
    printf("%lld",f[q*2][0]);
    return 0;
}
```