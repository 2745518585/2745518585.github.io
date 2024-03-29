---
title: 'CF1776C 题解'
date: 2023-03-23 15:43:43
tags: [CF]
description: 'SWERC 2022-2023 - Online Mirror (Unrated, ICPC Rules, Teams Preferred) C 题解'
---

### 题意

有一个长度为 $m$ 的序列，以及大小为 $n$ 的可重集。两个人依次进行操作：先手在可重集中选择一个数 $L$，再在序列上选择一段长度为 $L$ 的区间，满足区间内的所有位置均未被标记，然后从可重集重删除 $L$；后手在先手上一次选择的区间中选择一个位置并进行标记。如果先手无法选择一个合法的区间则失败，把可重集删完则获胜，求最终谁会获胜，并与交互库模拟这个过程。

### 解法

先考虑先手如何操作。我们可以考虑一个贪心策略，把区间长度从大到小排序，每次尝试放置最长的区间，并找到可以放置的间隔最小的两个标记位置中间放置。

考虑后手。后手进行标记的最终目的是让对方有一个区间放不进去，我们可以枚举这个区间的长度 $x$，显然在 $x$ 的倍数位置标记是最优方案。如果当前先手选的区间包含了 $x$ 的整数倍位置，那么就标记这个位置，否则这个区间长度小于 $x$，任意放置。

我们可以发现能标记 $x$ 整数倍位置的个数就是已选择区间中长度大于 $x$ 的区间个数，而使得长度大于等于 $x$ 的区间无法放置至少需要标记 $\lfloor \frac{m}{x} \rfloor$ 个，所以只需要判断长度大于等于 $x$ 的区间个数即可。

### code

{% contentbox type:success title:code %}
```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=100001;
int n,m,t,a[N];
bool h[N];
int solve()
{
    sort(a+1,a+m+1);
    for(int i=1;i<=m;++i)
    {
        if(n/a[i]<=m-i)
        {
            t=a[i];
            return 1;
        }
    }
    return 0;
}
pair<int,int> solve1(int p)
{
    h[p]=true;
    int x=0,y=1e9,z=0;
    for(int i=1;i<=n;++i)
    {
        if(h[i])
        {
            if(z>=a[m]&&z<y) x=i-z,y=z;
            z=0;
        }
        else ++z;
    }
    if(z>=a[m]&&z<y-x+1) x=n-z+1,y=z;
    --m;
    return make_pair(x,x+a[m+1]-1);
}
int solve2(pair<int,int> z)
{
    --m;
    int x=z.first,y=z.second;
    if(y-x+1<t) return x;
    for(int i=x;i<=y;++i)
    {
        if(i%t==0) return i;
    }
    return x;
}
int main()
{
    scanf("%d%d",&m,&n);
    for(int i=1;i<=m;++i)
    {
        scanf("%d",&a[i]);
    }
    if(solve()==0)
    {
        printf("Alessia\n");
        fflush(stdout);
        pair<int,int> z=solve1(0);
        printf("%d %d\n",z.second-z.first+1,z.first);
        fflush(stdout);
        while(m)
        {
            int x;
            scanf("%d",&x);
            pair<int,int> z=solve1(x);
            printf("%d %d\n",z.second-z.first+1,z.first);
            fflush(stdout);
        }
    }
    else
    {
        printf("Bernardo\n");
        fflush(stdout);
        while(m)
        {
            int x,y;
            scanf("%d%d",&x,&y);
            printf("%d\n",solve2(make_pair(y,y+x-1)));
            fflush(stdout);
        }
    }
    return 0;
}
```
{% endcontentbox %}



