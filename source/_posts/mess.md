---
title: '杂题.md'
date: 2024-05-01 00:00:00
tags: [杂题]
description: ' '
---

#### [QOJ6](https://qoj.ac/problem/6) 玛里苟斯

注意到一个集合的答案等于其线性基的答案。

$k \ge 3$ 暴力，$k=1,2$ 单独考虑。

#### [P6837](https://www.luogu.com.cn/problem/P6837) [QOJ1138](https://qoj.ac/problem/1138) Counting Mushrooms

根号分治，先查出根号个 $0/1$，再一次查询根号个。

精细实现，$2$ 次查 $5$ 个。

#### [QOJ1430](https://qoj.ac/problem/1430) Ineq

Pick 定理数凸包内点数

#### [P8985](https://www.luogu.com.cn/problem/P8985) [QOJ3270](https://qoj.ac/problem/3270) 魔塔 OL

贪心部分，前半段 $a_i \le b_i$，按 $a_i$ 升序，后半段按 $b_i$ 降序，反向操作可证。

转化为四维偏序，考虑对于高维偏序的一般做法：用 bitset 处理出落在这个前缀内的所有点的标号，那么一次查询相当于将每个维度对应的 bitset 求交。由于这里需要顺序递推，考虑分块，每块长 $\log$，块内 $2^B$ 处理，复杂度 $O(\frac{(n+q+V)n}{\log n})$。

#### [P8987](https://www.luogu.com.cn/problem/P8987) [QOJ3272](https://qoj.ac/problem/3272) 简单数据结构

注意到不降序列操作后仍然不降，且操作是全局的，考虑维护不降序列区间，整体二分可维护。

#### [QOJ4303](https://qoj.ac/problem/4303) New Level

题意：给定一个图和一个染色方案（$1 \sim k$），保证相邻两个点颜色不同，找出一个方案满足：

1. 相邻两个点颜色不同。
2. 只保留两点颜色编号差模 $k$ 同余 $1$ 的边后图仍然连通。

将边权设为两点颜色之差，将点权改为到 $1$ 的最短路。正确性易证。

#### [QOJ4891](https://qoj.ac/problem/4891) 树上的孤独

[题解](https://4182543731.github.io/post/LullabyforTiuLii/#d1-t3-42-%E6%A0%91%E4%B8%8A%E7%9A%84%E5%AD%A4%E7%8B%AC)

#### [QOJ4895](https://qoj.ac/problem/4895) Lovely Dogs

[题解](https://4182543731.github.io/post/LullabyforTiuLii/#d3-t1-9-lovely-dogs)

注意到 $f(i)$ 不为 $0$ 有 $f(i) = f(d)f(\frac{i}{d})$，转化为统计子树内质因子个数不超过一定值的 $v$ 的和，容斥后可维护。

#### [P9055](https://www.luogu.com.cn/problem/P9055) [QOJ4900](https://qoj.ac/problem/4900) 数列重排

[题解](https://4182543731.github.io/post/LullabyforTiuLii/#d4-t3-48-%E6%95%B0%E5%88%97%E9%87%8D%E6%8E%92)

对于一个 $k$，一个区间合法当且仅当 $0 \sim k-1$（称为关键数）都出现过，关键数依次排列显然最优：包含任意 $k$ 个关键数的区间均合法。非关键数仅能插到 $k$ 的整数倍位置和末尾。

#### [QOJ4906](https://qoj.ac/problem/4906) 球球

[题解](https://4182543731.github.io/post/LullabyforTiuLii/#d6-t3-19-%E7%90%83%E7%90%83)

分类讨论。用 $S_i$ 和 $T_i$ 分别表示人和分身在 $a_i$ 时另一个可以在的位置集合，几种操作都能简单维护。

#### [QOJ4909](https://qoj.ac/problem/4909) 《关于因为与去年互测zjk撞题而不得不改题这回事》

[题解](https://4182543731.github.io/post/LullabyforTiuLii/#d7-t3-29-%E5%85%B3%E4%BA%8E%E5%9B%A0%E4%B8%BA%E4%B8%8E%E5%8E%BB%E5%B9%B4%E4%BA%92%E6%B5%8Bzjk%E6%92%9E%E9%A2%98%E8%80%8C%E4%B8%8D%E5%BE%97%E4%B8%8D%E6%94%B9%E9%A2%98%E8%BF%99%E5%9B%9E%E4%BA%8B)

贪心部分，如果当前位为 $1$ 的个数大于等于 $k$，则只保留当前位为 $1$ 的数，答案这一位为 $1$，否则这一位为 $0$。

假设当前序列有序，则只需要判断第 $k$ 大的数，而如果当前位为 $0$，忽略当前位的第 $k$ 大一定在整个序列前 $2k$ 个。所以 $\log v$ 层的前 $k$ 大一定在整个序列前 $k \log v$ 个，取出来暴力即可。

取出链上前 $q$ 大可以每次取最大值后分成两条链。

#### [QOJ4914](https://qoj.ac/problem/4914) Slight Hope

$l$ 限制转为区间查，对 $r$ 分块。注意到一个区间构成的森林中作为根的节点深度相差至多为 $1$，可快速求出一个点所在根节点。

#### [QOJ4924](https://qoj.ac/problem/4924) 蜘蛛爬树

[题解](https://4182543731.github.io/post/LullabyforTiuLii/#d12-t3-44-%E8%9C%98%E8%9B%9B%E7%88%AC%E6%A0%91)

分类讨论。发现查询可以在凸包上二分解决。树剖线段树维护凸包，建树时凸包从儿子归并可以少掉排序的 $\log$。

#### [QOJ5040](https://qoj.ac/problem/5040) Dirichlet $k$-th root

[题解](https://blog.csdn.net/qq_21334057/article/details/104382333)

当 $f(1)=1$ 时，有 $f = g^{\text{inv}(k)}$。

#### [QOJ5047](https://qoj.ac/problem/5047) Permutation

[题解](https://www.cnblogs.com/zhouzhendong/p/ECfinal2019.html) J

每次按最小值分治。

#### [QOJ5171](https://qoj.ac/problem/5171) 理论出线
