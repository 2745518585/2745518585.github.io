---
title: 'P2774 方格取数问题'
date: 2022-04-17 11:25:18
tags: [网络流,最大流]
description: ' '
---

[P2774 方格取数问题](https://www.luogu.com.cn/problem/P2774)
## 分析
这实际上也是一道网络流的问题，但是和以前做的题都不一样，同时也很难想到怎么做。题目中要求任意两个相邻的方格不能同时取，如果我们把一个方格放在 A 组，另一个方块放在 B 组，那么所有和 A 中方格相邻的方格都要放在 B 组，和 B 中方格相邻的都要放在 A 组。我们可以发现，同一组中方格横纵坐标的和奇偶性相同，这样我们就可以直接分组了。于是我们就把这个图分成了两组，只有两组之间有关系，同一组的方格没有关系，是不是很像二分图？我们把所有相邻的方格都用一条边连上，问题转换为删去一些节点，使得两组之间连通，求出删去节点权值的最小值。
于是我们又可以自然而然地想到最小割（最小割=最大流），但是最小割删去的是边，而不是点。我们转化一下，将源点和 A 组所有点相连，汇点和 B 组所有点相连，容量均为该点对应的数，这样删去一条边的代价刚好是对应点的数值。但是我们不能删去两组之间的边，为了不删去这些边，把这些边的容量设为正无穷，就永远不可能删去这些边了。由于最小割 $=$ 最大流，所以我们只需要跑一遍最大流即可。注意只能由 A 组连向 B 组或 B 组连向 A 组，不能同时连。
建出来的图大概长这样：
![1](/post-images/P2774-solution-1.png)
## code
```cpp
#include<cstdio>
#include<algorithm>
#include<queue>
using namespace std;
typedef long long ll;
const int N=10001,M=1000001;
int n,m,p=1,s1,s2,t[N],t0[N],f[N];
ll q;
struct str
{
    int m,q;
    ll r;
}a[M];
void road(int x,int y,ll r)
{
    a[++p].m=y;
    a[p].q=t[x];
    t[x]=p;
    a[p].r=r;
}
bool bfs()
{
    queue<int> Q;
    Q.push(s1);
    for(int i=1;i<=s2;++i) f[i]=0;
    f[s1]=1;
    while(!Q.empty())
    {
        int k=Q.front();
        Q.pop();
        for(int i=t[k];i!=0;i=a[i].q)
        {
            if(f[a[i].m]==0&&a[i].r!=0)
            {
                f[a[i].m]=f[k]+1;
                Q.push(a[i].m);
            }
        }
    }
    return f[s2]!=0;
}
ll dfs(int x,ll r)
{
    if(x==s2) return r;
    ll s=0;
    for(int i=t0[x];i!=0;i=a[i].q)
    {
        t0[x]=i;
        if(f[a[i].m]==f[x]+1&&a[i].r!=0)
        {
            int z=dfs(a[i].m,min(r,a[i].r));
            if(z!=0)
            {
                a[i].r-=z;
                a[i^1].r+=z;
                r-=z;
                s+=z;
            }
            else f[a[i].m]=0;
            if(r==0) return s; 
        }
    }
    return s;
}
int main()
{
    scanf("%d%d",&m,&n);
    s1=m*n+1;
    s2=m*n+2;
    for(int i=1;i<=m;++i)
    {
        for(int j=1;j<=n;++j)
        {
            int r;
            scanf("%d",&r);
            q+=r;
            if((i+j)&1)
            {
                road(s1,(i-1)*n+j,r);
                road((i-1)*n+j,s1,0);
                if(i!=1)
                {
                    road((i-1)*n+j,(i-2)*n+j,1e18);
                    road((i-2)*n+j,(i-1)*n+j,0);
                }
                if(j!=1)
                {
                    road((i-1)*n+j,(i-1)*n+j-1,1e18);
                    road((i-1)*n+j-1,(i-1)*n+j,0);
                }
                if(i!=m)
                {
                    road((i-1)*n+j,i*n+j,1e18);
                    road(i*n+j,(i-1)*n+j,0);
                }
                if(j!=n)
                {
                    road((i-1)*n+j,(i-1)*n+j+1,1e18);
                    road((i-1)*n+j+1,(i-1)*n+j,0);
                }
            }
            else
            {
                road((i-1)*n+j,s2,r);
                road(s2,(i-1)*n+j,0);
            }
        }
    }
    ll r=0;
    while(bfs())
    {
        for(int i=1;i<=s2;++i) t0[i]=t[i];
        r+=dfs(s1,1e18);
    }
    printf("%lld",q-r);
    return 0;
}
```