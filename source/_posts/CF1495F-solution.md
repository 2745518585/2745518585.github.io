---
title: 'CF1495F 题解'
date: 2023-02-24 16:36:26
tags: [CF]
description: ' '
---

### 题意

有一排格子，每个格子有三个数 $p_i,a_i,b_i$，现在你要玩一个游戏，你站在第一个格子上，要跳到第 $n+1$ 个格子，$p_{n+1}=\infty$。有一个格子集合 $S$，你必须跳到 $S$ 中的所有格子(其它随意)。

假设你站在格子 $i$，下一步你可以选择 : 

- 花费 $a_i$ 的代价，跳到 $i+1$。

- 花费 $b_i$ 的代价，跳到 $i$ 后面第一个满足 $p_j>p_i$ 的位置 $j$。

一轮游戏的代价是跳的总代价。

$q$ 次询问，每次可以在上一次的 $S$ 中添加或删除一个元素。

### 思路

因为起点是 $1$，终点是 $n+1$，我们直接把这两个点塞进 $S$，并无法删除。

首先因为需要经过 $S$ 中的所有点，我们可以转换为 $S$ 中相邻两点的距离和，也就是 $dis(S_1,S_k) = \sum_{i=1}^{k-1} dis(S_{i-1},S_i)$。由于相邻两次询问只修改了一个点，所以相邻两次询问的答案只有 $O(1)$ 次询问的差距，比如第 $t$ 次添加一个点 $S_k$，就有：

$$ans_t = ans_{t-1} - dis(S_{t-1},S_{t+1}) + dis(S_{t-1},S_t) + dis(S_t,S_{t+1})$$

把询问离线下来后，就变成了 $O(n)$ 次询问 $dis(x,y)$。

我们把第一种走法和第二种走法放在一起看，看起来我们可以把左端点 $l$ 从 $n+1$ 移到 $1$，过程中处理跳跃 $l,r$ 的走法造成的贡献。如果当前情况下 $l$ 到 $r$ 的代价比直接走这个区间的代价小，那么就按原来的走，否则就走这个区间。但是这样并不对，因为我们按局部最优走，可能一下子走过了某个跳跃位置，导致结果不优。

但是我们还没有用到第二种走法的特殊之处，分析一下可以发现，第二种走法的区间之间要么包含要么不交。所以像上面那样走是不会跳过某个跳跃位置的。如果一个跳跃 $l,r$ 能造成贡献，那么 $l$ 到 $[r,n+1]$ 的代价都相应的减小相同的值 ，用树状数组即可维护。

### code

{% contentbox type:success title:code %}
```cpp
#include<cstdio>
#include<algorithm>
#include<vector>
#include<set>
using namespace std;
typedef long long ll;
const int N=1000001;
int n,m,c[N],d[N];
ll a[N],b[N],f[N],T[N];
set<int> Set;
struct str
{
    int x,u,t;
};
vector<str> e[N];
bool cmp(int x,int y)
{
    return c[x]>c[y];
}
void add(int x,ll k)
{
    while(x<=n+1)
    {
        T[x]+=k;
        x+=(x&-x);
    }
}
ll sum(int x)
{
    ll s=0;
    while(x>=1)
    {
        s+=T[x];
        x-=(x&-x);
    }
    return s;
}
int main()
{
    scanf("%d%d",&n,&m);
    for(int i=1;i<=n;++i)
    {
        scanf("%d",&c[i]);
    }
    for(int i=1;i<=n;++i)
    {
        scanf("%lld",&a[i]);
    }
    for(int i=1;i<=n;++i)
    {
        scanf("%lld",&b[i]);
    }
    c[n+1]=1e9;
    for(int i=n;i>=1;--i)
    {
        int x=i+1;
        while(c[x]<=c[i]) x=d[x];
        d[i]=x;
    }
    Set.insert(1);
    Set.insert(n+1);
    e[1].push_back((str){n+1,1,0});
    for(int i=1;i<=m;++i)
    {
        int x;
        scanf("%d",&x);
        if(x==1) continue;
        if(Set.count(x))
        {
            int z1=*(--Set.find(x)),z2=*(++Set.find(x));
            e[z1].push_back((str){x,-1,i});
            e[x].push_back((str){z2,-1,i});
            e[z1].push_back((str){z2,1,i});
            Set.erase(x);
        }
        else
        {
            Set.insert(x);
            int z1=*(--Set.find(x)),z2=*(++Set.find(x));
            e[z1].push_back((str){x,1,i});
            e[x].push_back((str){z2,1,i});
            e[z1].push_back((str){z2,-1,i});
        }
    }
    for(int i=n+1;i>=1;--i)
    {
        if(i<=n)
        {
            add(i+1,a[i]);
            ll z=sum(d[i]);
            if(b[i]<z) add(d[i],b[i]-z);
        }
        for(int j=0;j<e[i].size();++j)
        {
            f[e[i][j].t]+=e[i][j].u*sum(e[i][j].x);
        }
    }
    for(int i=1;i<=m;++i) f[i]+=f[i-1];
    for(int i=1;i<=m;++i)
    {
        printf("%lld\n",f[i]);
    }
    return 0;
}
```
{% endcontentbox %}

