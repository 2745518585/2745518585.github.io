---
title: 'NOI 2023 春季测试 T4 密码锁题解'
date: 2023-03-06 19:02:44
tags: ''
description: 'NOI 2023 春季测试 T4 密码锁题解'
---

## 题意

给你一个 $k$ 行 $n$ 列的矩阵（$k \leq 4$），每次可以选定一列进行一次循环位移，定义 $c(i)$ 为任意次操作后第 $i$ 行的最大值与最小值之差，求 $\max c(i)$ 的最小值。

## 提示

尝试二分答案。

考虑全局最大值一定是某一行的最大值，全局最小值一定是某一行最小值。

最大值和最小值在不同行一定不劣于在同一行。

## 思路

最大值最小，考虑二分答案。

接下来我们要判定 $\forall i,c(i)\leq x$。我们按 $k$ 从小到大解决。

$k=1$ 显然等于最大值减最小值。

假如我们已经钦定了某一行的最大值 $mx_i$，显然在这一行的每一数应该大于等于 $mx_i-x$。但是直接枚举显然不优。我们发现全局最大值 $mx$ 一定是某一行的最大值，由于行与行之间没有区别，我们直接钦定 $mx_1 = mx$。

同理，我们也可以发现全局最小值 $mn$ 也一定是某一行的最小值。如果把 $mx$ 和 $mn$ 放在同一行，答案 $mx-mn$ 显然达到了上界，所以把 $mx$ 和 $mn$ 不同行一定不劣。所以我们钦定 $mx_1 = mx,mn_2 = mn$，就可以判断每一列是否可能满足限制。至此，我们已经解决了 $k=2$。

对于 $k=3$ 的情况，我们可以钦定 $mx_1 = mx$，但是最小值在第二行或第三行实际上是不同的情况，所以我们需要枚举最小值所在的行，令剩下没有钦定的行为 $i$，钦定最小值为 $mn_i$。对于某一列，总共有三种情况，首先要去除不满足全局最大最小限制的情况，还要满足 $a_i \leq mn_i + x$，即
$$
a_i-x \leq mn_i \leq a_i
$$
总共有 $O(n)$ 个这样的式子，$mn_i$ 必须要满足每一列至少有一个式子成立。显然如果存在这样一个 $mn_i$，一定可以找到一个这一行的数满足每一列至少成立一个式子（即向上找到第一个上界）。问题转化为有 $n$ 种区间，求是否存在一个点使得每种区间都存在一个区间包含这个点。我们只需要使用扫描线，用一个数组记录当前点被第 $i$ 种区间包含了多少次即可。

对于 $k=4$ 的情况，我们用一样的方法，发现变量变成了两个，即有 $O(n)$ 组

{% raw %}
$$
\left\{
\begin{aligned}
a_i-x \leq mn_i \leq a_i\\
a_j-x \leq mn_j \leq a_j
\end{aligned}
\right.
$$
{% endraw %}

这样的式子，$mn_i$ 与 $mn_j$ 必须满足每一列至少有一组式子成立。转换过来就变成了二维的，有 $n$ 种矩阵，求平面上是否存在一个点使得每种矩阵都存在一个矩阵包含这个点。同样考虑扫描线，用线段树维护当前高度的一条线上每个点被多少种区间覆盖，和这个的全局最大值，但是这里不好去重，我采用的比较暴力的方法：由于每种矩阵最多只有 $4$ 个，所以我们可以容斥一下变成至多 $15$ 个矩阵。我们只需要判断被覆盖的区间数的最大值是不是 $n$ 即可。

## code

{% contentbox type:success title:code %}
```cpp
#include<cstdio>
#include<algorithm>
using namespace std;
const int N=1000001;
int n,m,k,q,mx,mn,a[N][4],e[N];
struct node
{
    int x1,x2,y1,y2;
    node() {}
    node(int x1,int x2,int y1,int y2):x1(x1),x2(x2),y1(y1),y2(y2) {}
}b[N][4];
struct line
{
    int x1,x2,h,u;
    line() {}
    line(int x1,int x2,int h,int u):x1(x1),x2(x2),h(h),u(u) {}
}c[N<<5];
bool cmp(line a,line b)
{
    return a.h<b.h;
}
struct tree
{
    int l,r,s,k;
}T[N<<2];
void pushup(int x)
{
    T[x].s=max(T[x<<1].s,T[x<<1|1].s);
}
void pushdown(int x)
{
    if(T[x].k==0) return;
    T[x<<1].s+=T[x].k;
    T[x<<1].k+=T[x].k;
    T[x<<1|1].s+=T[x].k;
    T[x<<1|1].k+=T[x].k;
    T[x].k=0;
}
void build(int x,int l,int r)
{
    T[x].l=l;
    T[x].r=r;
    T[x].s=T[x].k=0;
    if(l==r) return;
    int z=l+r>>1;
    build(x<<1,l,z);
    build(x<<1|1,z+1,r);
    pushup(x);
}
void add(int x,int l,int r,int k)
{
    if(l>r) return;
    if(T[x].l>=l&&T[x].r<=r)
    {
        T[x].s+=k;
        T[x].k+=k;
        return;
    }
    pushdown(x);
    int z=T[x].l+T[x].r>>1;
    if(l<=z) add(x<<1,l,r,k);
    if(r>z) add(x<<1|1,l,r,k);
    pushup(x);
}
void add_node(int u,node p1=node(1,3e4,1,3e4),node p2=node(1,3e4,1,3e4),node p3=node(1,3e4,1,3e4),node p4=node(1,3e4,1,3e4))
{
    int x1=max(max(p1.x1,p2.x1),max(p3.x1,p4.x1));
    int x2=min(min(p1.x2,p2.x2),min(p3.x2,p4.x2));
    int y1=max(max(p1.y1,p2.y1),max(p3.y1,p4.y1));
    int y2=min(min(p1.y2,p2.y2),min(p3.y2,p4.y2));
    if(x1<=x2&&y1<=y2)
    {
        c[++q]=line(x1,x2,y1,u);
        c[++q]=line(x1,x2,y2+1,-u);
    }
}
bool check()
{
    q=0;
    for(int i=1;i<=n;++i)
    {
        add_node(1,b[i][0]);
        add_node(1,b[i][1]);
        add_node(1,b[i][2]);
        add_node(1,b[i][3]);
        add_node(-1,b[i][0],b[i][1]);
        add_node(-1,b[i][0],b[i][2]);
        add_node(-1,b[i][0],b[i][3]);
        add_node(-1,b[i][1],b[i][2]);
        add_node(-1,b[i][1],b[i][3]);
        add_node(-1,b[i][2],b[i][3]);
        add_node(1,b[i][0],b[i][1],b[i][2]);
        add_node(1,b[i][0],b[i][1],b[i][3]);
        add_node(1,b[i][0],b[i][2],b[i][3]);
        add_node(1,b[i][1],b[i][2],b[i][3]);
        add_node(-1,b[i][0],b[i][1],b[i][2],b[i][3]);
    }
    sort(c+1,c+q+1,cmp);
    build(1,1,m);
    int x=1;
    for(int i=1;i<=m;++i)
    {
        while(x<=q&&c[x].h==i)
        {
            add(1,c[x].x1,c[x].x2,c[x].u);
            ++x;
        }
        if(T[1].s==n) return true;
    }
    return false;
}
int find(int x)
{
    return lower_bound(e+1,e+m+1,x)-e;
}
bool solve(int x)
{
    for(int i=1;i<=3;++i)
    {
        for(int j=1;j<=n;++j)
        {
            for(int k=0;k<=3;++k) b[j][k]=node(1,1,0,0);
        }
        for(int j=1;j<=n;++j)
        {
            for(int k=0;k<=3;++k)
            {
                if(mx-a[j][k]>x||a[j][(k+i)%4]-mn>x) continue;
                int z1=0,z2=0;
                if(i==1) z1=a[j][(k+2)%4],z2=a[j][(k+3)%4];
                if(i==2) z1=a[j][(k+1)%4],z2=a[j][(k+3)%4];
                if(i==3) z1=a[j][(k+1)%4],z2=a[j][(k+2)%4];
                b[j][k]=node(find(max(1,z1-x)),find(z1),find(max(1,z2-x)),find(z2));
            }
        }
        if(check()) return true;
    }
    return false;
}
namespace Solve2
{
    bool solve(int x)
    {
        for(int i=1;i<=n;++i)
        {
            bool u=false;
            for(int j=0;j<=1;++j)
            {
                if(mx-a[i][j]>x||a[i][(j+1)%2]-mn>x) continue;
                u=true;
            }
            if(!u) return false;
        }
        return true;
    }
}
namespace Solve3
{
    int c[N],q;
    struct str
    {
        int x,h,u;
        str() {}
        str(int x,int h,int u):x(x),h(h),u(u) {}
    }b[N];
    bool cmp(str a,str b)
    {
        return a.h<b.h;
    }
    bool check()
    {
        sort(b+1,b+q+1,cmp);
        for(int i=1;i<=n;++i) c[i]=0;
        int x=1,s=0;
        for(int i=1;i<=m;++i)
        {
            while(x<=q&&b[x].h==i)
            {
                if(c[b[x].x]==0) ++s;
                c[b[x].x]+=b[x].u;
                if(c[b[x].x]==0) --s;
                ++x;
            }
            if(s==n) return true;
        }
        return false;
    }
    bool solve(int x)
    {
        for(int i=1;i<=2;++i)
        {
            q=0;
            for(int j=1;j<=n;++j)
            {
                for(int k=0;k<=2;++k)
                {
                    if(mx-a[j][k]>x||a[j][(k+i)%3]-mn>x) continue;
                    int z=0;
                    if(i==1) z=a[j][(k+2)%3];
                    if(i==2) z=a[j][(k+1)%3];
                    b[++q]=str(j,find(max(1,z-x)),1);
                    b[++q]=str(j,find(z+1),-1);
                }
            }
            if(check()) return true;
        }
        return false;
    }
}
void abc()
{
    scanf("%d",&n);
    mx=1,mn=3e4;
    m=0;
    for(int i=0;i<=k-1;++i)
    {
        for(int j=1;j<=n;++j)
        {
            scanf("%d",&a[j][i]);
            e[++m]=a[j][i];
            mx=max(mx,a[j][i]);
            mn=min(mn,a[j][i]);
        }
    }
    if(k==1)
    {
        printf("%d\n",mx-mn);
        return;
    }
    sort(e+1,e+m+1);
    m=unique(e+1,e+m+1)-e-1;
    auto chk=[&](int x)
    {
        if(k==2) return Solve2::solve(x);
        if(k==3) return Solve3::solve(x);
        if(k==4) return solve(x);
        return false;
    };
    int l=0,r=3e4;
    while(l<r)
    {
        int z=l+r>>1;
        if(chk(z)) r=z;
        else l=z+1;
    }
    printf("%d\n",l);
}
int main()
{
    int T;
    scanf("%d%d",&T,&k);
    while(T--) abc();
    return 0;
}
```
{% endcontentbox %}

