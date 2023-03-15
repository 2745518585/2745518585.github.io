---
title: '面积并扫描线'
date: 2022-02-20 13:13:41
tags: [扫描线,线段树]
description: ' '
---

## 前言
模板题：[P5490 【模板】扫描线](https://www.luogu.com.cn/problem/P5490)
这是一道经典的题目，需要求出 $n$ 个矩形的面积并，直接开数组显然会炸，数学方法也很难，这时候就需要使用神奇的扫描线算法来解决了。
扫描线算法的基本思想就是用一条线从下往上扫过图形，计算出相邻两条横边之间的面积，然后求和即可。扫描线的过程中需要知道相邻两个横边之间的距离，以及这当前位置图形的宽度。我们只需要将横边排序就可以知道相邻两个横边之间的距离，而当前位置图形的宽度可以用线段树来维护。
## 线段树
线段树需要求出当前位置图形的宽度，我们要如何修改当前位置图形的宽度呢？我们看下图：
![1](https://img.ffis.me/images/2019/08/10/image.png)
![2](https://img.ffis.me/images/2019/08/10/image1cba39d5beb42edc.png)
（图片来自网络）
我们可以发现，扫过一个矩形的下边就会增加图形的宽度，而遇到一个图形的上边就会减少图形的宽度。我们可以拿括号序列来举例：如果一个位置前面的左括号个数等于右括号个数，那么这个位置就不在任何括号中。同理：如果一个位置扫过的矩形下边个数等于矩形上边个数，那么这个位置当前没有被图形覆盖，反之则被图形覆盖。我们只需要记录一个位置当前已扫过的矩形下边个数减去已扫过的矩形上边个数，我们就可以通过这个数是否为 $0$ 来判断这个位置是否被图形覆盖。
现在我们要记录一条横线上的每个位置的值，并且我们每次需要求出有多少个值不为 $0$ 的位置，我们就可以用线段树来实现，线段树维护当前区间值不为 $0$ 的位置个数：
![3](https://img.ffis.me/images/2019/08/10/image0a03aa15aca4877e.png)
不过我们需要维护的是两个相邻 $x$ 之间扫过的个数，而不是端点，是两个端点之间的线段，所以总共 $n$ 个点，只有 $n-1$ 个线段，线段树只需要维护 $n-1$ 个值。由于这些点之间可能间隔很大，所以还需要进行离散化，用 $b$ 数组记录离散化后的点原来的值，那么一个区间 $[l.r]$ 的长度就是 $b[T[x].r+1]-b[T[x].l]$。我们用一个变量 $t$ 来记录此区间被扫过的次数，如果不为 $0$ 那么此区间已经整体被覆盖。
这样我们就可以写出线段树了。
```cpp
void pushup(int x)
{
    if(T[x].t) T[x].s=b[T[x].r+1]-b[T[x].l];
    else T[x].s=T[x<<1].s+T[x<<1|1].s;
}
void build(int x,int l,int r)
{
    T[x].l=l;
    T[x].r=r;
    T[x].t=T[x].s=0;
    if(l==r) return;
    int z=l+r>>1;
    build(x<<1,l,z);
    build(x<<1|1,z+1,r);
}
void add(int x,ll l,ll r,int k)
{
    if(b[T[x].l]>=r||b[T[x].r+1]<=l) return;
    if(b[T[x].l]>=l&&b[T[x].r+1]<=r)
    {
        T[x].t+=k;
        pushup(x);
        return;
    }
    add(x<<1,l,r,k);
    add(x<<1|1,l,r,k);
    pushup(x);
}
```
## 排序
在扫描线算法中，我们总共需要两次排序：将端点的横坐标排序和横线的纵坐标排序。在进行排序之后，我们还需要将端点横坐标离散化和去重，用 STL 的 unique 函数就可以了。
```cpp
struct str
{
    ll l,r,h;
    int k;
}a[10000001];
struct tree
{
    int l,r,t;
    ll s;
}T[10000001];
bool cmp(str a,str b)
{
    return a.h<b.h;
}
int main()
{
    scanf("%d",&n);
    for(int i=1;i<=n;++i)
    {
        ll x1,y1,x2,y2;
        scanf("%lld%lld%lld%lld",&x1,&y1,&x2,&y2);
        a[i*2-1]=(str){x1,x2,y1,1};
        a[i*2]=(str){x1,x2,y2,-1};
        b[i*2-1]=x1;
        b[i*2]=x2;
    }
    n<<=1;
    sort(a+1,a+n+1,cmp);
    sort(b+1,b+n+1);
    m=unique(b+1,b+n+1)-(b+1);
}
```
## 扫描线
有了上面的所有准备，我们就可以写出扫描线了。我们只需要从第 $1$ 个横边扫到第 $n*2-1$  个横边即可，每一个区间的面积就是当前图形的宽度乘上这个横线到下一根横线的距离。用一个变量统计这些面积的和，最终的答案就是这个变量。
```cpp
build(1,1,m-1);
ll s=0;
for(int i=1;i<n;++i)
{   
    add(1,a[i].l,a[i].r,a[i].k);
    s+=T[1].s*(a[i+1].h-a[i].h);
}
printf("%lld",s);
```
## code
```cpp
#include<cstdio>
#include<algorithm>
using namespace std;
typedef long long ll;
int n,m;
ll b[10000001];
struct str
{
    ll l,r,h;
    int k;
}a[10000001];
struct tree
{
    int l,r,t;
    ll s;
}T[10000001];
bool cmp(str a,str b)
{
    return a.h<b.h;
}
void pushup(int x)
{
    if(T[x].t) T[x].s=b[T[x].r+1]-b[T[x].l];
    else T[x].s=T[x<<1].s+T[x<<1|1].s;
}
void build(int x,int l,int r)
{
    T[x].l=l;
    T[x].r=r;
    T[x].t=T[x].s=0;
    if(l==r) return;
    int z=l+r>>1;
    build(x<<1,l,z);
    build(x<<1|1,z+1,r);
}
void add(int x,ll l,ll r,int k)
{
    if(b[T[x].l]>=r||b[T[x].r+1]<=l) return;
    if(b[T[x].l]>=l&&b[T[x].r+1]<=r)
    {
        T[x].t+=k;
        pushup(x);
        return;
    }
    add(x<<1,l,r,k);
    add(x<<1|1,l,r,k);
    pushup(x);
}
int main()
{
    scanf("%d",&n);
    for(int i=1;i<=n;++i)
    {
        ll x1,y1,x2,y2;
        scanf("%lld%lld%lld%lld",&x1,&y1,&x2,&y2);
        a[i*2-1]=(str){x1,x2,y1,1};
        a[i*2]=(str){x1,x2,y2,-1};
        b[i*2-1]=x1;
        b[i*2]=x2;
    }
    n<<=1;
    sort(a+1,a+n+1,cmp);
    sort(b+1,b+n+1);
    m=unique(b+1,b+n+1)-(b+1);
    build(1,1,m-1);
    ll s=0;
    for(int i=1;i<n;++i)
    {   
        add(1,a[i].l,a[i].r,a[i].k);
        s+=T[1].s*(a[i+1].h-a[i].h);
    }
    printf("%lld",s);
    return 0;
}
```