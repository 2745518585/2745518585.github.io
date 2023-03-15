---
title: 'P4072 [SDOI2016]征途'
date: 2022-04-04 08:13:41
tags: [动态规划,单调队列,斜率优化dp]
description: ' '
---

[P4072 [SDOI2016]征途](https://www.luogu.com.cn/problem/P4072)
## 分析
这道题是分了层的斜率优化 dp，它与普通的斜率优化的不同之处就是它限制了只能分成 $m$ 段，所以我们需要先枚举走多少段。对于每一段，重新开一个单调队列存储点，但是这个单调队列维护的每一个点上一次的信息。假设当前走了 $i$ 段，那么单调队列维护的就是 $i-1$ 段的信息。我们不需要在算出 $i$ 天的情况时就建立单调队列，我们只需要在更新 $i+1$ 段的时候依次将第 $i$ 天的信息入队即可。
为了节约空间和方便建立单调队列，这里使用滚动数组存储信息。
## code
```cpp
#include<cstdio>
#include<algorithm>
using namespace std;
const int M=10001,N=10001;
int n,m,a[N],Q[N*2],T=0,R=0,f[N],g[N];
int abc(int x,int y)
{
    if(a[x]==a[y]) return 1e9;
    return ((g[x]+a[x]*a[x])-(g[y]+a[y]*a[y]))/(a[x]-a[y]);
}
int main()
{
    scanf("%d%d",&n,&m);
    for(int i=1;i<=n;++i)
    {
        int x;
        scanf("%d",&x);
        a[i]=a[i-1]+x;
        g[i]=1e9;
    }
    g[0]=0;
    for(int i=1;i<=m;++i)
    {
        T=0,R=0;
        for(int j=1;j<=n;++j)
        {
            while(T<R&&abc(Q[T],Q[T+1])<2*a[j]) ++T;
            f[j]=g[Q[T]]+(a[j]-a[Q[T]])*(a[j]-a[Q[T]]);
            while(T<R&&abc(Q[R],j)<abc(Q[R-1],Q[R])) --R;
            Q[++R]=j;
        }
        for(int j=1;j<=n;++j) g[j]=f[j];
    }
    printf("%d",f[n]*m-a[n]*a[n]);
    return 0;
}
```