---
title: 'P2936 [USACO09JAN]Total Flow S'
date: 2022-04-13 13:47:30
tags: [网络流,最大流]
description: ' '
---

[P2936 [USACO09JAN]Total Flow S](https://www.luogu.com.cn/problem/P2936)
## 分析
最大流的模板题，注意输入。
## code
```cpp
#include<iostream>
#include<cstdio>
#include<algorithm>
#include<queue>
using namespace std;
int m,p=1,s1,s2,t[10001],f[10001];
struct str
{
    int m,q,r;
}a[1000001];
void road(int x,int y,int r)
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
    for(int i=0;i<=127;++i) f[i]=0;
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
int dfs(int x,int r)
{
    if(x==s2) return r;
    int s=0;
    for(int i=t[x];i!=0;i=a[i].q)
    {
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
        }
    }
    return s;
}
int main()
{
    scanf("%d",&m);
    s1='A';
    s2='Z';
    for(int i=1;i<=m;++i)
    {
        char x,y;
        x=getchar();
        while((x<'A'||x>'Z')&&(x<'a'||x>'z')) x=getchar();
        y=getchar();
        while((y<'A'||y>'Z')&&(y<'a'||y>'z')) y=getchar();
        int r;
        scanf("%d",&r);
        road(int(x),int(y),r);
        road(int(y),int(x),0);
    }
    int r=0;
    while(bfs())
    {
        r+=dfs(s1,1e9);
    }
    printf("%d",r);
    return 0;
}
```