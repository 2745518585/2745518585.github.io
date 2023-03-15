---
title: 'P5785 [SDOI2012]任务安排'
date: 2022-04-05 09:15:30
tags: [动态规划,斜率优化dp]
description: ' '
---

[P5785 [SDOI2012]任务安排](https://www.luogu.com.cn/problem/P5785)
## 分析
这道题和往常的斜率优化 dp 不太一样。我们设计的状态要做到一维，就只能储存当前处理到了哪个任务，已经分了多少批就无法储存，但是处理的批数不同会导致启动时间不同，会对后面的 dp 产生影响。这里有一个非常巧妙的方法，我们知道后面所有任务的费用系数，就可以把启动时间对后面所有任务的影响提前计算，也就是在费用中加上 $s \times (b[n]-b[j])$，其中 $a$ 是完成所需时间的前缀和，$b$ 是费用系数的前缀和。于是我们可以得到转移方程：
$$f[i]=f[j]+a[i] \times (b[i]-b[j])+s \times (b[n]-b[j])$$
$$f[j]-s \times b[j]=a[i] \times b[j]+f[i]-a[i] \times b[i]-s \times b[n]$$
但是还有一个问题，数据范围中写道 $|T_i| \leqslant 2^8$，什么意思，说明 $T_i$ 可能是负的，也就是 $a$ 不单调。所以我们就不能用单调队列了，需要每一次在队列中二分查找，直到前一条线段斜率比它小，后一条线段斜率大于等于它。我们还是需要维护整个队列的“下凸性”，如果队尾存在“上凸”，还是要将队尾出队。
## code
```cpp
#include<cstdio>
#include<algorithm>
using namespace std;
typedef long long ll;
const int N=300001;
int n,m,Q[N*2],T=0,R=0;
ll a[N],b[N],f[N];
ll abc(int x,int y)
{
    if(b[x]==b[y]) return 1e18;
    return ((f[x]-m*b[x])-(f[y]-m*b[y]))/(b[x]-b[y]);
}
int main()
{
    scanf("%d%d",&n,&m);
    for(int i=1;i<=n;++i)
    {
        int x,y;
        scanf("%d%d",&x,&y);
        a[i]=a[i-1]+x;
        b[i]=b[i-1]+y;
    }
    Q[++R]=0;
    for(int i=1;i<=n;++i)
    {
        int l=T,r=R;
        while(l<r)
        {
            int z=l+r>>1;
            if(abc(Q[z],Q[z+1])<a[i]) l=z+1;
            else if(abc(Q[z],Q[z+1])>a[i]) r=z;
            else
            {
                l=z;
                break;
            }
        }
        f[i]=f[Q[l]]+a[i]*(b[i]-b[Q[l]])+m*(b[n]-b[Q[l]]);
        while(T<R&&abc(Q[R],i)<abc(Q[R-1],Q[R])) --R;
        Q[++R]=i;
    }
    printf("%lld",f[n]);
    return 0;
}
```