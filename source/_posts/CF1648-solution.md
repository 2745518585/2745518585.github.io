---
title: 'Codeforces Round 775 A ~ D 题解'
date: 2023-03-22 19:57:54
tags: [CF]
description: 'Codeforces Round 775 (Div. 1, based on Moscow Open Olympiad in Informatics) A ~ D 题解'
---

## A. Weird Sum

### 题意

给定一个 $m \times n$ 的矩阵，求任意一对值相同的元素的曼哈顿距离和。

$m \times n \leq 10^5$

### 解法

枚举值，取出所有这个值的元素，用 vector 记录即可。$x$ 从小到大扫描，依次加入元素，用树状数组维护当前元素和前面所有的和。具体的，树状数组以 $y$ 为下标，分别记录元素个数、$y$ 坐标加 $x$ 坐标、$y$ 坐标减 $x$ 坐标，分别计算 $y$ 坐标比当前元素小的和大的贡献。

### code

```cpp
#include<cstdio>
#include<algorithm>
#include<vector>
using namespace std;
typedef long long ll;
const int N=1000001;
int m,n;
ll T1[N],T2[N],T3[N];
void add(ll *T,int x,ll k)
{
    while(x<=n) T[x]+=k,x+=x&-x;
}
ll sum(ll *T,int x)
{
    ll s=0;
    while(x>=1) s+=T[x],x-=x&-x;
    return s;
}
struct str
{
    int x,y;
    str(){}
    str(int x,int y):x(x),y(y){}
};
vector<str> a[N];
int main()
{
    scanf("%d%d",&m,&n);
    for(int i=1;i<=m;++i)
    {
        for(int j=1;j<=n;++j)
        {
            int x;
            scanf("%d",&x);
            a[x].push_back(str(i,j));
        }
    }
    ll s=0;
    for(int i=1;i<=100000;++i)
    {
        for(auto j:a[i])
        {
            s+=sum(T1,j.y)*(j.x+j.y)-sum(T2,j.y);
            s+=(sum(T3,n)-sum(T3,j.y))-(sum(T1,n)-sum(T1,j.y))*(j.y-j.x);
            add(T1,j.y,1);
            add(T2,j.y,j.y+j.x);
            add(T3,j.y,j.y-j.x);
        }
        for(auto j:a[i])
        {
            add(T1,j.y,-1);
            add(T2,j.y,-(j.y+j.x));
            add(T3,j.y,-(j.y-j.x));
        }
    }
    printf("%lld",s);
    return 0;
}
```

## B. Integral Array

### 题意

给定一个数组 $a$，求是否对于任意 $a$ 中的两个元素 $x,y,x \geq y$，$\lfloor \frac{x}{y} \rfloor$ 都出现在数组中。

$n \leq 10^6,1 \leq a_i \leq 10^6$

### 解法

考虑枚举没有出现在数组中的数 $z$，如果数组中存在 $x,y,x \geq y$ 满足 $\lfloor \frac{x}{y} \rfloor = z$，这个数组就不不合法。考虑枚举 $y$，判断是否存在  $x \in [yz,(y+1)z-1]$ 即可。由于 $1 \leq y \leq \lfloor \frac{x}{z} \rfloor$，所以 $y$ 的总个数是调和级数。

### code

```cpp
#include<cstdio>
#include<algorithm>
using namespace std;
const int N=2000001;
int n,m,a[N],b[N],c[N];
int main()
{
    int T;
    scanf("%d",&T);
    while(T--)
    {
        scanf("%d%d",&n,&m);
        for(int i=1;i<=m;++i) b[i]=c[i]=0;
        for(int i=1;i<=n;++i)
        {
            scanf("%d",&a[i]);
            ++b[a[i]];
        }
        for(int i=1;i<=m;++i) c[i]=c[i-1]+b[i];
        sort(a+1,a+n+1);
        bool u=true;
        for(int i=1;i<=m;++i)
        {
            if(b[i]!=0) continue;
            for(int j=1;j<=m/i;++j)
            {
                if(b[j]&&c[min(j*(i+1)-1,m)]-c[j*i-1]>0)
                {
                    u=false;
                    break;
                }
            }
            if(!u) break;
        }
        if(u) printf("Yes\n");
        else printf("No\n");
    }
    return 0;
}
```

## C. Tyler and Strings

### 题意

给定两个字符串 $s$ 和 $t$，求重排 $s$ 后有多少种情况 $s$ 的字典序小于 $t$。

$|s|,|t| \leq 2 \times 10^5$，字符集 $2 \times 10^5$。

### 解法

枚举 $s$ 第一个比 $t$ 小的地方 $i$，$[1,i-1]$ 的部分 $s$ 和 $t$ 一定相同，如果无法满足情况数为 $0$，下面讨论的情况已钦定此条件满足。如果能够只需要满足 $s_i < t_i$，后面任意。假如我们钦定了 $i$ 选什么，后面的总情况就是 $w=(\sum a_j)! \prod (a_j!)^{-1}$，$a_i$ 为字符 $i$ 剩下的个数。考虑上 $s_i$，选定 $s_i$ 的影响就是 $a_{s_i}$ 减 $1$，总情况变为 $w=((\sum a_j)-1)! \prod ((a_j-[j=s_i])!)^{-1}$，可以发现对于不同的 $s_i$，变化的项只有 $1$ 个，我们令 $w'=((\sum a_j)-1)! \prod (a_j!)^{-1}$，选定 $s_i$ 的贡献即为 $w=w'\times (a_j!) \times ((a_j-1)!)^{-1}$，我们只需要统计后面这一块的和 $t$ 即可，用树状数组维护。

$i$ 每向后移动一位，$a_{s_{i-1}}$ 都会减少 $1$，$w$ 和 $t$ 也会相应改变，但是每次只改变一项，直接维护即可。

### code

```cpp
#include<cstdio>
#include<algorithm>
using namespace std;
typedef long long ll;
const int N=1000001;
const ll P=998244353;
int m,n,a[N],b[N],h[N];
ll jc[N],T[N];
void exgcd(ll a,ll b,ll &x,ll &y)
{
    if(b==0) x=1,y=0;
    else exgcd(b,a%b,y,x),y-=a/b*x;
}
ll inv(ll a)
{
    ll x,y;
    exgcd(a,P,x,y);
    x=(x%P+P)%P;
    return x;
}
void add(int x,ll k)
{
    while(x<=200000) T[x]+=k,x+=x&-x;
}
ll sum(int x)
{
    ll s=0;
    while(x>=1) s+=T[x],x-=x&-x;
    return s;
}
int main()
{
    jc[0]=1;
    for(int i=1;i<=200000;++i) jc[i]=(jc[i-1]*i)%P;
    scanf("%d%d",&m,&n);
    for(int i=1;i<=m;++i)
    {
        int x;
        scanf("%d",&x);
        ++a[x];
    }
    for(int i=1;i<=n;++i)
    {
        scanf("%d",&b[i]);
    }
    ll w=jc[m];
    for(int i=1;i<=200000;++i)
    {
        if(a[i]>0)
        {
            add(i,jc[a[i]]*inv(jc[a[i]-1])%P);
            w=w*inv(jc[a[i]])%P;
        }
    }
    ll s=0;
    for(int i=1;i<=min(m,n);++i)
    {
        w=w*inv(jc[m-i+1])%P*jc[m-i]%P;
        s=(s+w*sum(b[i]-1)%P)%P;
        if(a[b[i]]>0)
        {
            w=w*jc[a[b[i]]]%P;
            add(b[i],-(jc[a[b[i]]]*inv(jc[a[b[i]]-1])%P));
            --a[b[i]];
            if(a[b[i]]>0)
            {
                add(b[i],jc[a[b[i]]]*inv(jc[a[b[i]]-1])%P);
                w=w*inv(jc[a[b[i]]])%P;
            }
        }
        else
        {
            w=0;
            break;
        }
    }
    if(m<n&&w!=0) s=(s+1)%P;
    printf("%lld",s);
    return 0;
}
```

## D. Serious Business

### 题意

给定一个 $3 \times n$ 的矩阵，每个位置有一个值。第一行和第三行可以走，第二行不可以。共有 $q$ 个操作，第 $i$ 个操作可以用 $k_i$ 的代价把第二行 $[l_i,r_i]$ 变为可以走，求经过的位置的价值和减去总代价的最大值。

### 解法

显然在第二行经过的位置是一段连续的区间，所以选择的操作的区间也是连续的，且除了第一段和最后一段区间之外全部会走满。我们把总价值拆成两部分：$a_l-b_{l-1}$ 与 $b_r+(c_n-c_{r-1})$，其中 $a,b,c$ 分别是三行的价值， $l,r$ 是进入和离开第二行的位置。我们考虑把第一部分贡献转移到最后一段区间：首先把 $a_j-b_{j-1}$ 转移到 $f_j$，由于第一个和中间的区间都是走到了区间末尾的，所以区间 $i$ 的贡献就是把 $\max\limits_{l_i \leq j \leq r_i} f_j-k_i$ 贡献到 $f_{r_i}$。用线段树维护。

然后我们需要把在同一个区间的两种贡献合并，也就是对于区间 $i$，对于任意 $j,p \in [l_i,r_i],j \leq p$，把 $f_j + b_p+(c_n-c_{p-1}) -p_i$ 贡献到总答案。这里需要保证 $j \leq p$，也可以用线段树维护，每次把左子树最大的 $f_j$ 和右子树最大的 $b_p+(c_p-c_{p-1})$ 相加贡献到答案即可。

### code

```cpp
#include<cstdio>
#include<algorithm>
#include<vector>
using namespace std;
typedef long long ll;
const int N=1000001;
int n,m;
ll a[N],b[N],c[N];
struct str
{
    int x,y,k;
    str(){}
    str(int x,int y,int k):x(x),y(y),k(k){}
};
vector<str> d[N],e;
struct tree
{
    int l,r;
    ll s,t1,t2,s2;
}T[N<<2];
void pushup(tree &T,tree T1,tree T2)
{
    T.s=max(T1.s,T2.s);
    T.s2=max(T1.t1+T2.t2,max(T1.s2,T2.s2));
    T.t1=max(T1.t1,T2.t1);
    T.t2=max(T1.t2,T2.t2);
}
void build(int x,int l,int r)
{
    T[x].l=l;
    T[x].r=r;
    if(l==r)
    {
        T[x].s=a[l]-b[l-1];
        T[x].t1=a[l]-b[l-1];
        T[x].t2=b[l]+(c[n]-c[l-1]);
        T[x].s2=T[x].t1+T[x].t2;
        return;
    }
    int z=l+r>>1;
    build(x<<1,l,z);
    build(x<<1|1,z+1,r);
    pushup(T[x],T[x<<1],T[x<<1|1]);
}
void add(int x,int q,ll k)
{
    if(T[x].l==T[x].r)
    {
        T[x].s=max(T[x].s,k);
        T[x].t1=max(T[x].t1,k);
        T[x].s2=T[x].t1+T[x].t2;
        return;
    }
    int z=T[x].l+T[x].r>>1;
    if(q<=z) add(x<<1,q,k);
    else add(x<<1|1,q,k);
    pushup(T[x],T[x<<1],T[x<<1|1]);
}
ll sum(int x,int l,int r)
{
    if(T[x].l>=l&&T[x].r<=r)
    {
        return T[x].s;
    }
    int z=T[x].l+T[x].r>>1;
    ll s=-1e18;
    if(l<=z) s=max(s,sum(x<<1,l,r));
    if(r>z) s=max(s,sum(x<<1|1,l,r));
    return s;
}
tree sum2(int x,int l,int r)
{
    if(T[x].l>=l&&T[x].r<=r)
    {
        return T[x];
    }
    int z=T[x].l+T[x].r>>1;
    tree s;
    if(l<=z&&(!(r>z))) s=sum2(x<<1,l,r);
    else if((!(l<=z))&&r>z) s=sum2(x<<1|1,l,r);
    else pushup(s,sum2(x<<1,l,r),sum2(x<<1|1,l,r));
    return s;
}
int main()
{
    scanf("%d%d",&n,&m);
    for(int i=1;i<=n;++i)
    {
        scanf("%lld",&a[i]);
        a[i]+=a[i-1];
    }
    for(int i=1;i<=n;++i)
    {
        scanf("%lld",&b[i]);
        b[i]+=b[i-1];
    }
    for(int i=1;i<=n;++i)
    {
        scanf("%lld",&c[i]);
        c[i]+=c[i-1];
    }
    for(int i=1;i<=m;++i)
    {
        int x,y,k;
        scanf("%d%d%d",&x,&y,&k);
        d[y].push_back(str(x,y,k));
        e.push_back(str(x,y,k));
    }
    build(1,1,n);
    for(int i=1;i<=n;++i)
    {
        ll s=-1e18;
        for(auto j:d[i])
        {
            s=max(s,sum(1,j.x,j.y)-j.k);
        }
        if(i<n) add(1,i+1,s);
    }
    ll s=-1e18;
    for(auto i:e)
    {
        s=max(s,sum2(1,i.x,i.y).s2-i.k);
    }
    printf("%lld",s);
    return 0;
}
```

