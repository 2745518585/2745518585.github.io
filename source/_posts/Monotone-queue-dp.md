---
title: '单调队列优化dp'
date: 2022-04-03 21:09:16
tags: [动态规划,单调队列,单调队列优化dp]
description: ' '
---

## 前言
经典题：[P2627 [USACO11OPEN]Mowing the Lawn G](https://www.luogu.com.cn/problem/P2627)
如果 dp 中每一个状态 $f[i]$ 都需要由 $f[j]$ 推过来，形如 $f[i]=\max(f[j]+x\times a[i]+y\times a[j])(i-k\leqslant j < i)$ 或 $f[i]=\min(f[j]+x\times a[i]+y\times a[j])(i-k\leqslant j < i)$，这类转移方程的特点是转移方程中有与 $i$ 相关的项和与 $j$ 相关的项的和。如果用朴素方法，就需要枚举 $i,j$ 复杂度为 $O(n^2)$。于是我们就需要一个方法来优化它。
## 基本思路
但是有没有发现，如果我们改写一下转移方程，就变成了 $f[i]-x\times a[i]=\min(f[j]+y\times a[j])(i-k\leqslant j < i)$，对于一个固定的 $i$，左边是定值，我们只需要找到 $[i-k,i)$ 中 $f[j]+y\times a[j]$ 的最小值（最大值）即可。如何实现？显然，可以用单调队列来实现，我们可以类比之前学过的单调队列优化背包问题，其实实质是一样的。
于是问题就转化为了求出一个长度为 $k$ 的区间的最小值（最大值），具体实现方法如下：枚举 $i$ ，对于每个 $i$，作如下操作。首先弹出队头，如果队头元素超出了区间范围就一直弹出。然后弹出队尾，如果队尾元素比当前元素大（小）就一直弹出队尾。取队首元素为 $j$，更新 $f[i]$。最后将 $i$ 入队。 
注意，这里建议手写队列而不是使用双端队列 deque，因为 deque 的常数实在是太大了。
## code
$#8195; 此代码为例题 [P2627](https://www.luogu.com.cn/problem/P2627) 的代码。
```cpp
#include<cstdio>
#include<algorithm>
using namespace std;
typedef long long ll;
const int N=100001;
int n,m,a[N],Q[N],T=0,R=-1;
ll b[N],f[N][2];
int main()
{
    scanf("%d%d",&n,&m);
    for(int i=1;i<=n;++i)
    {
        scanf("%d",&a[i]);
        b[i]=b[i-1]+a[i];
    }
    Q[++R]=0;
    for(int i=1;i<=n;++i)
    {
        f[i][0]=max(f[i-1][0],f[i-1][1]);
        while(T<=R&&i-Q[T]>m) ++T;
        while(T<=R&&f[Q[R]][0]-b[Q[R]]<=f[i][0]-b[i]) --R;
        f[i][1]=f[Q[T]][0]+b[i]-b[Q[T]];
        Q[++R]=i;
    }
    printf("%lld",max(f[n][0],f[n][1]));
    return 0;
}
```