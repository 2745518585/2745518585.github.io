---
title: 'P3648 [APIO2014]序列分割'
date: 2022-04-04 09:33:01
tags: [动态规划,单调队列,斜率优化dp]
description: ' '
---

[P3648 [APIO2014]序列分割](https://www.luogu.com.cn/problem/P3648)
## 分析
这道题也是一个分层斜率优化 dp，但是一眼看上去毫无头绪，我们会发现每一次操作的位置可以在上一次操作之前。但是我们来推一下：有三个数 $a,b,c$，如果我们先分开 $a,b$，再分开 $b.c$，得到的结果为 $a \times b+(a+b) \times c=a\times b+b\times c+c\times a$；如果我们先分开 $b,c$，再分开 $a,b$，得到的结果为 $b \times c+(b+c) \times a=a\times b+b\times c+c\times a$。这两种情况的结果是一样的，说明最终结果与切的顺序无关，只与切的位置有关。我们就假设切的位置递增，就可以用斜率优化 dp 了，$f[i]$ 表示最后一次切的位置是 $i-1$ 与 $i$ 即可。
另外，这道题要求求出最优情况的方案，我们只需要用一个 $h$ 数组来记录，$h[i][j]$ 表示切了 $i$ 次，最后一次切的位置是$j-1$ 与 $j$ 的情况中上一个切的位置。由于这里不管顺序，最后直接倒序输出也可以。
## code
```cpp
#include<cstdio>
#include<algorithm>
using namespace std;
typedef long long ll;
const int N=100001,M=201;
int n,m,Q[N*2],h[M][N],T=0,R=0;
ll a[N],f[N],g[N];
ll abc(int x,int y)
{
    if(a[x]==a[y]) return 1e18;
    return ((g[x]-a[x]*a[x])-(g[y]-a[y]*a[y]))/(a[x]-a[y]);
}
int main()
{
    scanf("%d%d",&n,&m);
    for(int i=1;i<=n;++i)
    {
        int x;
        scanf("%d",&x);
        a[i]=a[i-1]+x;
    }
    for(int i=1;i<=m;++i)
    {
        T=0,R=0;
        Q[0]=0;
        for(int j=1;j<=n;++j)
        {
            while(T<R&&abc(Q[T],Q[T+1])>-a[j]) ++T;
            f[j]=g[Q[T]]+a[Q[T]]*(a[j]-a[Q[T]]);
            h[i][j]=Q[T];
            while(T<R&&abc(Q[R],j)>abc(Q[R-1],Q[R])) --R;
            Q[++R]=j;
        }
        for(int j=1;j<=n;++j) g[j]=f[j];
    }
    printf("%lld\n",f[n]);
    for(int i=m,j=h[m][n];i>=1;j=h[--i][j]) printf("%d ",j);
    return 0;
}
```