---
title: 'Codeforces Round 692 (Div. 1, based on Technocup 2021 Elimination Round 3) A ~ C 题解'
date: 2023-03-21 16:06:45
tags: [CF]
description: ' '
---

## A. Peaceful Rooks

### 题意

给定一个 $n \times n$ 的棋盘和 $m$ 个棋子，任意两个棋子不在同一行或同一列。每次可以水平或竖直移动一个棋子，且移动后仍然任意两个棋子不在同一行或同一列，求把所有棋子移动到主对角线上所需的最少次数。

$n \leq 10^5$

### 解法

假设我们现在想把在 $(x,y)$ 的棋子移动到 $(x,x)$，如果 $x$ 列上有棋子 $(z,x)$，我们就需要先移走这个棋子，显然可以直接尝试移动到 $(z,z)$。棋子的支配关系会形成一些链或环，链所需步数就是棋子个数，环所需步数是个数 $+1$，并查集统计即可。注意判断本来就在对角线上的棋子。

### code

```cpp
#include<cstdio>
#include<algorithm>
using namespace std;
const int N=1000001;
int n,m,fa[N];
bool h[N];
int find(int x)
{
    if(fa[x]==x) return x;
    return fa[x]=find(fa[x]);
}
int main()
{
    int T;
    scanf("%d",&T);
    while(T--)
    {
        scanf("%d%d",&n,&m);
        for(int i=1;i<=n;++i) fa[i]=i;
        int s=0;
        for(int i=1;i<=m;++i)
        {
            int x,y;
            scanf("%d%d",&x,&y);
            if(x!=y) ++s;
            else fa[x]=0;
            fa[find(x)]=find(y);
        }
        for(int i=1;i<=n;++i)
        {
            if(find(i)==i) ++s;
        }
        printf("%d\n",s-(n-m));
    }
    return 0;
}
```

## B. Grime Zoo

### 题意

给定一个由 `0`、`1` 和 `?` 组成的字符串 $S$ 和参数 $x,y$，你需要在所有字符为 `?` 的位置填入字符 `0` 或者 `1`。

我们规定这个字符串的价值为：`01` 子序列的数量 $\times x + $ `10` 子序列的数量 $\times y$。

求字符串价值最小值。

$|S| \leq 10^5,0 \leq x,y \leq 10^6$

### 解法

对于一个 `?`，如果填 `0`，贡献为 $y \times $ 前面 $1$ 的个数 $+$ $x \times $ 后面 $1$ 的个数，填 `1` 的贡献为 $x \times $ 前面 $0$ 的个数 $+$ $y \times $ 后面 $0$ 的个数。由于越靠后的 `?`，前面的 `0` 和 `1` 个数单调不降，后面的 `0` 和 `1` 个数单调不增，所以只可能是 `00..11` 或 `11..00` 的情况。

假定是 `00..11` 的情况，我们可以先算出全部填 `1` 的价值，然后从前往后把 `?` 变为 `0`，每次处理变化的贡献即可。`11..00` 的情况同理。

### code

```cpp
#include<cstdio>
#include<algorithm>
#include<cstring>
using namespace std;
typedef long long ll;
const int N=1000001;
int n,b1[N],b2[N],b3[N],c1[N],c2[N],c3[N];
ll k1,k2;
char a[N];
int main()
{
    scanf("%s%lld%lld",a+1,&k1,&k2);
    n=strlen(a+1);
    for(int i=1;i<=n;++i)
    {
        b1[i]=b1[i-1];
        b2[i]=b2[i-1];
        b3[i]=b3[i-1];
        if(a[i]=='0') ++b1[i];
        if(a[i]=='1') ++b2[i];
        if(a[i]=='?') ++b3[i];
    }
    for(int i=n;i>=1;--i)
    {
        c1[i]=c1[i+1];
        c2[i]=c2[i+1];
        c3[i]=c3[i+1];
        if(a[i]=='0') ++c1[i];
        if(a[i]=='1') ++c2[i];
        if(a[i]=='?') ++c3[i];
    }
    ll w=0;
    for(int i=1;i<=n;++i)
    {
        if(a[i]=='0') w+=(b2[i-1]+b3[i-1])*k2;
        else w+=b1[i-1]*k1;
    }
    ll s=w;
    for(int i=1;i<=n;++i)
    {
        if(a[i]=='?')
        {
            w-=(b1[i-1]+b3[i-1])*k1+c1[i+1]*k2;
            w+=b2[i-1]*k2+(c2[i+1]+c3[i+1])*k1;
        }
        s=min(s,w);
    }
    w=0;
    for(int i=1;i<=n;++i)
    {
        if(a[i]=='0'||a[i]=='?') w+=b2[i-1]*k2;
        else w+=(b1[i-1]+b3[i-1])*k1;
    }
    s=min(s,w);
    for(int i=1;i<=n;++i)
    {
        if(a[i]=='?')
        {
            w-=(b2[i-1]+b3[i-1])*k2+c2[i+1]*k1;
            w+=b1[i-1]*k1+(c1[i+1]+c3[i+1])*k2;
        }
        s=min(s,w);
    }
    printf("%lld",s);
    return 0;
}
```

## C. Poman Numbers

### 题意

给定一个只含小写字母的字符串 $S$。

定义 $f(S)$。如果 $|S|=1$，设这个小写字母的是第 $t$ 个（`a` 为 $0$，`b` 为 $1$，`z` 为 $25$），$f(S)=2^t$。否则 $f(S)=-f(S[1,m])+f(S[m+1,|S|])$，$m$ 在 $[1,|S|-1]$ 中自由选择。

求是否存在一种构造 $m$ 的方案（每一步的 $m$ 可以不同）使得 $f(S)=T$。

### 解法

我们观察 $S$ 每一位最后可能的符号。首先最后一位在每一步中都是 $+$，所以符号一定是 $+$。倒数第二位后面只有一位，所有只可能有一次 $-$，最终符号一定是 $-$。倒数第三位就两种均可，我们可以猜测除了最后两位位符号任取。

我们先把最后两位的贡献在 $T$ 中减去，接下来就是给一些 $2$ 的次幂定符号，使得和为 $T$。我们从大到小排序，当前的数是 $x$，剩下的数的和 $T'< 0$，$+x$ 一定比 $-x$ 更优，因为如果 $-x$ 后仍然能使最后 $T' = 0$，又因为是 $2$ 的次幂，所以后面的数一定可以拼出 $x$，$+x$ 也有解。同理 $T'>0$ 则 $-x$。

### code

```cpp
#include<cstdio>
#include<algorithm>
using namespace std;
typedef long long ll;
const int N=1000001;
int n;
ll k;
char a[N];
int main()
{
    scanf("%d%lld%s",&n,&k,a+1);
    k+=(1ll<<(a[n-1]-'a'))-(1ll<<(a[n]-'a'));
    sort(a+1,a+n-1);
    for(int i=n-2;i>=1;--i)
    {
        if(k<=0) k+=(1ll<<(a[i]-'a'));
        else k-=(1ll<<(a[i]-'a'));
    }
    if(k==0) printf("Yes\n");
    else printf("No\n");
    return 0;
}
```

