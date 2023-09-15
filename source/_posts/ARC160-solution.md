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

{% contentbox type:success title:code %}
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
{% endcontentbox %}

## B - Triple Pair

### 题意

求有多少对正整数 $(x,y,z)$ 满足 $xy,yz,zx \leq n$，对 $998244353$ 取模。

$n \leq 10^9$

### 解法

考虑整除分块，先枚举 $x$，显然对于 $\frac{n}{x}$ 相等的所有 $x$，$y,z$ 的个数相等，设 $f_i$ 为 $y,z \leq i,yz \leq n$ 的个数，知道了 $f$ 就可以快速求答案。

枚举 $i$，对 $i$ 整除分块，$f_i$ 等于上一块也就是 $f_{\frac{n}{\frac{n}{i-1}}}$ 加上 $y,z$ 其中一个等于 $i$ 的情况，注意减去 $y,z$ 都等于 $i$ 的情况和特判 $i=1$。复杂度 $O(\sqrt{n})$。

### code

{% contentbox type:success title:code %}
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
{% endcontentbox %}

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

{% contentbox type:success title:code %}
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
{% endcontentbox %}

## D - Mahjong

### 题意

求有多少个长为 $N$，和为 $M$ 的序列 $A$，使得可以通过以下两种操作（次数无限制）将序列变为 $0$：

1. 在 $A$ 中选一个元素，将其减去 $K$（$K$ 给定）。
2. 在 $A$ 中选取长度为 $K$ 的子串，子串中每个元素减去 $1$。

### 解法

$K$ 不整除 $M$ 显然无解，所以限制即为总操作次数为 $\frac{M}{K}$。

我们发现在同一个子串上做 $K$ 次操作二，等于每个位置上做一次操作一，于是我们大胆猜测如果限制以某一个位置开始的操作二不超过 $K-1$ 次，那么操作位置和原始序列一一对应。证明显然，假设从前往后确定操作的个数，当前位置 $x$ 的值减去 $[x-k+1,x-1]$ 中操作二的个数后 $\mod K$ 的余数就是操作二的个数，因为前面的操作的贡献已经计算，后面的操作不会对这个位置产生影响。

题目转换为：构造长度为 $2N-K+1$ 的序列，其中前 $N-K+1$ 个数小于等于 $K-1$。考虑容斥，钦定一些数大于 $K-1$，剩下的随便放，详见代码。

### code

{% contentbox type:success title:code %}
```cpp
#include<cstdio>
#include<algorithm>
using namespace std;
typedef long long ll;
const ll P=998244353;
ll n,m,k;
void exgcd(ll a,ll b,ll &x,ll &y)
{
    if(b==0) x=1,y=0;
    else exgcd(b,a%b,y,x),y-=a/b*x;
}
ll inv(ll a)
{
    a=(a%P+P)%P;
    ll x,y;
    exgcd(a,P,x,y);
    return (x%P+P)%P;
}
ll C(ll a,ll b)
{
    if(a<b) return 0;   
    ll s=1;
    for(ll i=a-b+1;i<=a;++i) s=s*(i%P)%P;
    for(ll i=1;i<=b;++i) s=s*inv(i%P)%P;
    return s;
}
int main()
{
    scanf("%lld%lld%lld",&n,&m,&k);
    if(m%k!=0)
    {
        printf("0");
        return 0;
    }
    m/=k;
    ll s=0;
    for(ll i=0;i<=n-k+1;++i)
    {
        if(i%2==0) s=(s+C(n-k+1,i)*C(m-i*k+n*2-k,n*2-k)%P)%P;
        else s=(s-C(n-k+1,i)*C(m-i*k+n*2-k,n*2-k)%P)%P;
    }
    printf("%lld",(s%P+P)%P);
    return 0;
}
```
{% endcontentbox %}

## E - Make Biconnected

### 题意

给你一棵由无向边组成的**二叉树**，树上每个点有权值 $w_i$。你可以把两个点之间连无向边，如果将 $u$ 与 $v$ 连边，代价是 $w_u+w_v$。请给出一种连边方式，使得连边后，图中去掉任何一个点仍然联通，即图是一个点双连通图。在此基础上，你要使代价最小。

### 解法

显然可以发现每个叶子节点至少连一条边，我们尝试将叶子和叶子配对，于是分奇偶讨论：

- 偶数个叶子：叶子间可以两两匹配。如果两个叶子连了边，那么去掉的点在这两个叶子之间的路径上的情况都连通，因此在钦定了一个度数不为 $1$ 的节点为根后，我们希望匹配的叶子的 $LCA$ 深度尽量小。直接考虑 $LCA$ 都为根的情况，也就是说任意一对叶子要在不同的子树中，即最大的子树的叶子个数不超过 $\frac{k}{2}$（$k$ 为叶子个数）。显然任何一种情况都可以找到这么一个根。
- 奇数个叶子：假设无法匹配的叶子为 $x$，其他叶子按偶数情况匹配，我们会发现最后没有覆盖到的位置只有 $x$ 向上到最近一个三度点，因此我们只需要在这之外找到一个最小的点与之匹配即可。我们考虑钦定与这个叶子匹配的点 $y$（非叶子），容易发现如果 $x$ 和 $y$ 路径上包含三度点（不包含 $y$），这个 $x$ 就是合法的。显然除了只有一个三度点的情况下，任何一个点 $y$ 都能找到叶子与之匹配，因此 $y$ 就是全局最小点。只有一个三度点的情况下找最小的非三度点即可。

### code


{% contentbox type:success title:code %}
```cpp
#include<cstdio>
#include<algorithm>
#include<vector>
#include<queue>
using namespace std;
typedef long long ll;
const int N=1000001;
int n,m,b[N],l[N],f[N],fa[N];
bool g[N],h[N];
vector<int> a[N],c[N];
vector<pair<int,int>> ans;
void dfs0(int x)
{
    if(l[x]==1) f[x]=1;
    else f[x]=0;
    for(auto i:a[x])
    {
        if(i==fa[x]||g[i]) continue;
        fa[i]=x;
        dfs0(i);
        f[x]+=f[i];
    }
}
void dfs1(int x,vector<int> &c)
{
    if(l[x]==1) c.push_back(x);
    for(auto i:a[x])
    {
        if(i==fa[x]||g[i]) continue;
        dfs1(i,c);
    }
}
void dfs2(int x,int fa)
{
    h[x]=true;
    if(l[x]>=3) return;
    for(auto i:a[x])
    {
        if(i==fa) continue;
        dfs2(i,x);
    }
}
void dfs3(int x,int fa)
{
    if(l[x]>=3) return;
    g[x]=true;
    for(auto i:a[x])
    {
        if(i==fa) continue;
        dfs3(i,x);
    }
}
struct cmp
{
    bool operator()(int x,int y)
    {
        return c[x].size()<c[y].size();
    }
};
int main()
{
    int T;
    scanf("%d",&T);
    while(T--)
    {
        scanf("%d",&n);
        for(int i=1;i<=n;++i) a[i].clear(),c[i].clear(),l[i]=f[i]=fa[i]=h[i]=g[i]=0;
        ans.clear();
        for(int i=1;i<=n;++i)
        {
            scanf("%d",&b[i]);
        }
        for(int i=1;i<=n-1;++i)
        {
            int x,y;
            scanf("%d%d",&x,&y);
            a[x].push_back(y);
            a[y].push_back(x);
            ++l[x],++l[y];
        }
        m=0;
        for(int i=1;i<=n;++i)
        {
            if(l[i]==1) ++m;
        }
        if(m%2==1)
        {
            int u=0;
            for(int i=1;i<=n;++i)
            {
                if(l[i]>=3) ++u;
            }
            int o=0;
            if(u==1)
            {
                for(int i=1;i<=n;++i)
                {
                    if(l[i]<3&&(b[i]<b[o]||o==0)) o=i;
                }
            }
            else
            {
                for(int i=1;i<=n;++i)
                {
                    if(b[i]<b[o]||o==0) o=i;
                }
            }
            h[o]=true;
            for(auto i:a[o]) dfs2(i,o);
            int z=0;
            for(int i=1;i<=n;++i)
            {
                if(l[i]==1&&h[i]==false)
                {
                    z=i;
                    break;
                }
            }
            dfs3(z,0);
            ans.push_back(make_pair(o,z));
        }
        for(int i=1;i<=n;++i)
        {
            if(g[i]) continue;
            l[i]=0;
            for(auto j:a[i])
            {
                if(!g[j]) ++l[i];
            }
        }
        m=0;
        for(int i=1;i<=n;++i)
        {
            if(g[i]) continue;
            if(l[i]==1) ++m;
        }
        for(int i=1;i<=n;++i)
        {
            if(!g[i])
            {
                dfs0(i);
                break;
            }
        }
        int q=0;
        for(int i=1;i<=n;++i)
        {
            if(g[i]) continue;
            int w=0;
            for(auto j:a[i])
            {
                if(j!=fa[i]&&!g[j]) w=max(w,f[j]);
            }
            w=max(w,m-f[i]);
            if(w<=m/2) q=i;
        }
        fa[q]=0;
        dfs0(q);
        priority_queue<int,vector<int>,cmp> Q;
        for(auto i:a[q])
        {
            if(g[i]) continue;
            dfs1(i,c[i]);
            Q.push(i);
        }
        if(l[q]==1)
        {
            c[q].push_back(q);
            Q.push(q);
        }
        while(!Q.empty())
        {
            int k1=Q.top();
            Q.pop();
            int k2=Q.top();
            Q.pop();
            if(c[k1].size()==0) break;
            ans.push_back(make_pair(c[k1].back(),c[k2].back()));
            c[k1].pop_back(),c[k2].pop_back();
            Q.push(k1),Q.push(k2);
        }
        printf("%d\n",ans.size());
        for(auto i:ans)
        {
            printf("%d %d\n",i.first,i.second);
        }
    }
    return 0;
}
```
{% endcontentbox %}