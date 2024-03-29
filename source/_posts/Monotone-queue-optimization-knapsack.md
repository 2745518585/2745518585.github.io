---
title: '多重背包（单调队列优化）'
date: 2022-02-14 17:10:39
tags: [动态规划,背包,单调队列,单调队列优化dp]
description: ' '
---

## 前言
多重背包问题的时间复杂度为 $O(nm^2)$，复杂度很高，所以我们需要将其优化。其中一种办法就是使用单调队列优化，可以使复杂度达到 $O(nm)$。
## 单调队列
单调队列的一个元素有两个值：元素的值和位置（下标），单调队列会保证队首元素是原数列中值最小（或最大）的。单调队列的作用可以看下面的模板题：
[P1886 滑动窗口 /【模板】单调队列](https://www.luogu.com.cn/problem/P1886)
有一个长为 $n$ 的序列 $a$，以及一个大小为 $k$ 的窗口。现在这个从左边开始向右滑动，每次滑动一个单位，求出每次滑动后窗口中的最大值和最小值。
了解了单调队列的用途，我们以样例为例来看它如何实现。STL中的双端队列可以实现单调队列，但是常数不得不说有一点大，所以一般单调队列都用手写队列来实现。
$\text{1 3 -1 -3 5 3 6 7}$
首先定义两个队列 $p$ 与 $q$（也可以定义结构体队列），$p$ 中是每个元素在原数列的位置，$q$ 中是每个元素的值。
(1) 将 $1$ 入队，$q = \{ 1 \},p = \{ 1 \}$，此时队首元素为 $1$；
(2) 将 $3$ 入队，由于 $3 > 1$，所以 $q = \{ 1,3 \},p = \{ 1,2 \}$，此时队首元素为 $1$；
(3) 将 $-1$ 入队，由于 $-1 < 3,-1 < 1$，所以 $1$ 和 $3$ 都出队，$q = \{ -1 \},p = \{ 3 \}$，此时队首元素为 $-1$；
(4) 将 $-3$ 入队，同理 $-3 < -1$，所以将 $-1$ 出队，此时$q = \{ -3 \},p = \{ 4 \}$，队首元素为 $-3$；
(5) 将 $5$ 入队，此时$q = \{ -3,5 \},p = \{ 4,5 \}$，队首元素为 $-3$；
(6) 将 $3$ 入队，由于 $3 < 5$，所以$q = \{ -3,3 \},p = \{ 4,6 \}$，队首元素为 $-3$；
(7) 将 $6$ 入队，由于 $4 \leqslant 7 - 3$，也就是 $-3$ 已经不在窗口中了，所以弹出 $-3$ ，此时$q = \{ 3,6 \},p = \{ 6,7 \}$，队首元素为 $3$；
(8) 将 $7$ 入队，此时$q = \{ 3,6,7 \},p = \{ 6,7,8 \}$，队首元素为 $3$；
可以观察到每一次操作的队首元素都是当前窗口中的最小值（除了(1)(2)，因为此时已经入队的元素个数少于窗口大小）。我们可以简单总结以下这些操作：设元素总数为 $n$，窗口大小为 $m$，对于一个即将入队的元素 $x_i$ ，如果队尾元素满足 $x_i < q_{back}$，那么弹出队尾元素，如果队首元素对应的在原数列中的位置 $p_{front} \leqslant i - m$，那么弹出队首元素，然后将 $x_i$ 加入到 $q$ 的队尾，$i$ 加入到 $p$ 的队尾，如果 $x_i \geqslant m$，则队首元素就是当前窗口中的最小元素。
同理，我们也可以推出最大值的求法。下面上代码：
``` cpp
#include<cstdio>
using namespace std;
int n,m,a[1000001],q[1000001],p[1000001],T,R;
int main()
{
    scanf("%d%d",&n,&m);
    for(int i=1;i<=n;++i)
    {
        scanf("%d",&a[i]);
    }
    T=0,R=-1;
    for(int i=1;i<=n;++i)
    {
        while(T<=R&&p[T]<=i-m) ++T;
        while(T<=R&&q[R]>a[i]) --R;
        q[++R]=a[i];
        p[R]=i;
        if(i>=m) printf("%d ",q[T]);
    }
    printf("\n");
    T=0,R=-1;
    for(int i=1;i<=n;++i)
    {
        while(T<=R&&p[T]<=i-m) ++T;
        while(T<=R&&q[R]<a[i]) --R;
        q[++R]=a[i];
        p[R]=i;
        if(i>=m) printf("%d ",q[T]);
    }
    return 0;
}
```
## 单调队列优化多重背包
单调队列如何能和背包扯上关系的？设这件物品体积为 $v$，价值为 $w$，数量为$k$，我们来看一下多重背包的状态转移方程：
$f[m] = \max(f[m], f[m-v]+w, f[m-2\times v]+2\times w, f[m-3\times v]+3\times w,\cdots)$
将 $m$ 换为其他数，我们就可以得到：
$f[j]=f[j]$
$f[j+v]=\max(f[j]+w,f[j+v])$
$f[j+2\times v]=\max(f[j]+2\times w,f[j+v]+w,f[j+2\times v])$
$f[j+3\times v]=\max(f[j]+3\times w,f[j+v]+2\times w,f[j+2\times v]+w,f[j+3\times v])$
稍加转换，可得：
$f[j+0\times v]=\max(f[j])$
$f[j+1\times v]=\max(f[j],f[j+v]-w)+w$
$f[j+2\times v]=\max(f[j],f[j+v]-w,f[j+2\times v]-2\times w)+2\times w$
$f[j+3\times v]=\max(f[j],f[j+v]-w,f[j+2\times v]-2\times w,f[j+3\times v]-3\times w)+3\times w$
是不是惊人的相似。
这样就可以得到：
$f[j+k\times v]=\max(f[j],f[j+v]-w,f[j+2\times v]-2\times w,f[j+3\times v]-3\times w,\cdots,f[j+k\times v]-k\times w)+k\times w$
我们就可以看成有一个大小为 $k$的窗口在数列上扫过，每一个状态对应一个窗口。这样这个问题就成功地转换成了单调队列的问题了。
``` cpp
#include<cstdio>
using namespace std;
int n,m,q[1000001],p[1000001],T=-1,R=0,f[100001];
int main()
{
    scanf("%d%d",&n,&m);
    for(int i=1;i<=n;++i)
    {
        int a,b,c;
        scanf("%d%d%d",&b,&a,&c);
        for(int j=0;j<a;++j)
        {
            T=0;
            R=-1;
            for(int k=j;k<=m;k+=a)
            {
                while(T<=R&&k-p[T]>a*c) ++T;
                while(T<=R&&q[R]+(k-p[R])/a*b<=f[k]) --R;
                p[++R]=k;
                q[R]=f[k];
                f[k]=q[T]+(k-p[T])/a*b;
            }
        }
    }
    printf("%d",f[m]);
    return 0;
}
```