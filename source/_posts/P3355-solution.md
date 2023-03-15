---
title: 'P3355 骑士共存问题'
date: 2022-04-17 11:42:36
tags: [网络流,最大流]
description: ' '
---

[P3355 骑士共存问题](https://www.luogu.com.cn/problem/P3355)
## 分析
这道题和 P2774 是一个类型的题，只是分组方式不同。我们来看两个互相攻击的点有什么特点：$(x,y) \Leftrightarrow (x+1,y+2)$，$(x,y) \Leftrightarrow (x-1,y+2)$，$(x,y) \Leftrightarrow (x-2,y+1)$，$\cdots$。不难发现，两个相互攻击的点横纵坐标之和奇偶性不同，我们就可以通过横纵坐标的奇偶性来分组。这道题还多了障碍物的限制，只要有障碍物的点不向外连边即可。
## code
```cpp
#include<cstdio>
#include<algorithm>
#include<queue>
using namespace std;
typedef long long ll;
const int N=50001,M=2000001;
int n,m,p=1,s1,s2,t[N],t0[N],f[N];
short dx[8]={-2,-1,1,2,2,1,-1,-2},dy[8]={-1,-2,-2,-1,1,2,2,1};
bool b[201][201];
struct str
{
    int m,q;
    ll r;
}a[M];
bool check(int x,int y)
{
    return x>=1&&x<=n&&y>=1&&y<=n;
}
int sum(int x,int y)
{
    return (x-1)*n+y;
}
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
    scanf("%d%d",&n,&m);
    s1=n*n+1;
    s2=n*n+2;
    for(int i=1;i<=m;++i)
    {
        int x,y;
        scanf("%d%d",&x,&y);
        b[x][y]=true;
    }
    for(int i=1;i<=n;++i)
    {
        for(int j=1;j<=n;++j)
        {
            if(b[i][j]==true) continue;
            if((i+j)&1)
            {
                road(s1,sum(i,j),1);
                road(sum(i,j),s1,0);
                for(int k=0;k<=7;++k)
                {
                    if(check(i+dx[k],j+dy[k]))
                    {
                        road(sum(i,j),sum(i+dx[k],j+dy[k]),1e18);
                        road(sum(i+dx[k],j+dy[k]),sum(i,j),0);
                    }
                }
            }
            else
            {
                road(sum(i,j),s2,1);
                road(s2,sum(i,j),0);
            }
        }
    }
    ll r=0;
    while(bfs())
    {
        for(int i=1;i<=s2;++i) t0[i]=t[i];
        r+=dfs(s1,1e18);
    }
    printf("%lld",n*n-m-r);
    return 0;
}
```