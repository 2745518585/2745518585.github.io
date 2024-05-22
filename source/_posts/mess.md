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


#### [QOJ5305](https://qoj.ac/problem/5305) Oscar is All You Need

分类讨论。

注意到可以通过 $op(i,2),op(1,i)$ 将末尾数移至第 $i$ 个数后，考虑每次将插入到前面来排序。由于每段非空，显然不能将通过这种方法交换最后两个数，需特殊考虑。

#### [QOJ5308](https://qoj.ac/problem/5308) RPG Pro League

左边四个点分别代表一队中 $4$ 个位置，右边 $7$ 个点表示玩家类型，求出最多多少组对于左部点的完美匹配。考虑完美匹配的判断：Hall 定理（$\forall S \subseteq X,|N(S)| \ge |S|$），那么能拆分出的完美匹配组数就是 $\min(\frac{|N(S)|}{S}),S \subseteq X$。

考虑修改，显然加入/删除 $x$ 后最多只会删除/加入一个人，且这个人为所属类型集合中最大值/集合外最小值，枚举即可。

#### [QOJ5309](https://qoj.ac/problem/5309) Guess Cycle Length

根号做法显然。考虑查询得到的编号一定在 $1$ 到 $n$ 之间，可以通过随机查询一些位置来得到一个较大的下限，然后使用根号做法即可。

#### [QOJ5312](https://qoj.ac/problem/5312) Levenshtein Distance

记 $f_{i,j}$ 表示 $S_{1,x}$ 与 $T_{1,x+j}$ 距离为 $i$，$x$ 最大值，ST 表维护 $\text{LCP}(S_{x,|S|},T_{y,|T|})$，对于 $T$ 每个起始位置 $k^2$ 转移一次即可。

#### [QOJ5418](https://qoj.ac/problem/5418) Color the Tree

法 1：考虑每层分开考虑，对当前层的所有点建虚树后可 dp。

法 2：$f_{x,i}$ 表示 $x$ 子树中第 $i$ 层全部染色的代价，长链剖分可做。

#### [QOJ5421](https://qoj.ac/problem/5421) Factories Once More

记 $f_{x,i}$ 表示 $x$ 子树中选了 $i$ 个点的最大值，转移 $f_{x,i} = \max(f_{x,i-j}+f_{y,j}+w j (k-j))$，注意到 $f$ 是上凸函数（上凸函数的 $(\max,+)$ 卷积仍为上凸函数）。考虑维护单调的差分数组，$(\max,+)$ 卷积为合并两个差分数组，$f_{x,i} \rightarrow f_{x,i+1}$ 即在最高点插入一个 $0$，同时还需要支持给差分数组加上等差数列，平衡树启发式合并维护。

#### [QOJ5423](https://qoj.ac/problem/5423) Perfect Matching

转化为一个二分图第 $i$ 条边连接 $a_i-i$ 与 $a_i+i$，问能否将边分成 $\frac{n}{2}$ 对使得每队边有公共点。

对于一个连通块 dfs，通过调整当前点到父亲的边可以使得其余连接到这个点的边均匹配，到父亲的边一定会在父亲点匹配。

#### [QOJ5425](https://qoj.ac/problem/5425) Proposition Composition

分类讨论。

1. 删其中某一条边即不连通。维护未被覆盖的边数即可。
2. 其中一条边覆盖另一条。当且仅当链上的那条边只被一条边覆盖，容易维护。
3. 两条边都在链上。当且仅当所有额外边要么同时覆盖要么同时不覆盖。考虑维护一个集合满足集合内任意两条边满足条件，线段树维护区间内最小前驱/最大后继，暴力分裂，使用启发式分裂（修改分裂后较小的一个集合）维护所属集合。

#### [QOJ5519](https://qoj.ac/problem/5519) Count Hamiltonian Cycles

尽量使前缀中两种点匹配但不能全部匹配，贪心即可。

#### [QOJ5523](https://qoj.ac/problem/5523) Graph Problem With Small $n$

记 $f_i$ 表示以 $1$ 开头经过 $i$ 中的点的路径可能的结束点，容易转移与求答案。

#### [QOJ5528](https://qoj.ac/problem/5528)  Least Annoying Constructive Problem

先考虑奇数情况：把所有边分成 $n$ 组，其中第 $i$ 组为 ${(i-1,i+1),(i-2,i+2),\dots,(i-n/2,i+n/2)}$（所有数对 $n$ 取模）。依次输出 $1$ 到 $n$ 组中的边即可，容易证明。

偶数情况先去掉 $n$ 按奇数的方法分组，然后每组末尾加上 $(i,n)$，容易证明。

#### [QOJ6308](https://qoj.ac/problem/6308) Magic

注意到 $l,r$ 互不相同，那么对于有交但不包含的两个区间 $[l_1,r_1)$ 与 $[l_2,r_2)$（$l_1 < l_2$），要使 $a_{l_1} \neq a_{l_1+1}$，显然 $[l_1,r_1)$ 需要比 $[l_2,r_2)$ 后操作，$r_2$ 同理，即 $l_1$ 与 $r_2$ 至多只能有一个满足条件。

转化为二分图最大独立集，使用 bitset 可以通过。

#### [QOJ6501](https://qoj.ac/problem/6501) Graph Partitioning

对于一条边 $(x,y)$，在第一棵树上时 $x$ 是 $y$ 的父亲，第二棵树上时 $y$ 是 $x$ 的父亲，相当于选择第一棵树的 $x$ 或第二棵树的 $y$，每个点仅能被一条边选择。有解当且仅当每个连通块边数等于点数，即基环树，方案为 $2$。

#### [QOJ6837](https://qoj.ac/problem/6837) AC Automaton

记 $f$ 为 $u$ 子树中某种字符的个数，$g$ 为 $u$ 祖先中某种字符的个数，答案即为 $\sum\limits_{s_u='A'} f_{u,\{'C','?'\}} + \sum\limits_{s_u='?'} \max(f_{u,\{'C','?'\}}-g_{u,\{'A','?'\}},0) = \sum\limits_{s_u='A'} F_u + \sum\limits_{s_u='?'} \max(G_u,0)$。考虑维护 $F$ 与 $\max(G,0)$。

对询问分块，块内在虚树上处理，容易维护。

#### [QOJ6842](https://qoj.ac/problem/6842) Popcount Words

容易想到建立 AC 自动机后暴力跳。考虑倍增，注意到 $2|\text{popcount}(k)$ 时 $[k2^n,(k+1)2^n)$ 与 $[0,2^n)$ 相同，否则恰好相反。记 $w_{n,0/1}$ 表示 $[0,2^n) / [0,2^n)$ 取反，那么任意一个区间可以拆分为 $\log$ 段 $w$。

#### [QOJ6846](https://qoj.ac/problem/6846) Wiring Engineering

确定 $a$ 和 $c$ 后容易 $n^2$ 转移，注意到方程与 $a,c$ 无关，可以转化为 $(a,c)$ 到 $(b+1,d+1)$ 的最长路。注意到 $n$ 较小，$q$ 较大，考虑分治，将两维中左右端点差距较大的一个分治。枚举中点 $m$ 对应的点 $i$，表示 $m$ 左边的点连接 $i$ 左边的点，$m$ 右边的点连接 $i$ 右边的点，容易处理出跨过中点的询问的答案，复杂度 $O(n^3+nq)$。

#### [P9061](https://www.luogu.com.cn/problem/P9061) [QOJ7436](https://qoj.ac/problem/7436) Optimal Ordered Problem Solver

考虑维护轮廓线，使用平衡树维护轮廓线上的点。轮廓线外的点的贡献是坐标与时间的三维偏序，当询问点在轮廓线外时，右上方的区域一定与轮廓线无交，那么分别求出上方、右方、右上方的点数即可，复杂度 $O(n \log n)$。

#### [QOJ7736](https://qoj.ac/problem/7736) Red Black Tree

记 $f_{x,i}$ 表示 $x$ 到其子树内所有叶子黑点个数均为 $i$ 的最小代价，可以证明 $f_x$ 是凸函数，因此可以维护差分数组，$(\min,+)$ 卷积只需要合并两个差分数组，当前点的贡献为插入一个 $-1$ 或 $1$，可以用两个 vector 分别维护负数和正数部分。

#### [QOJ8343](https://qoj.ac/problem/8343) 玩游戏

[题解](https://www.ceoi2016.ro/wp-content/uploads/2016/08/trick-solution.pdf)

考虑用两张牌映射到这 $n$ 个数的和 $s$，首先将每个数减去 $s$，我们希望找到两个和为 $0$ 的牌，这样两个数的和就是 $2s$。两两匹配后共有 $n+1$ 组，显然不可行，考虑将 $(0,1,2n),(2,n,2n-1),(4,n+1,2n-3)$ 组成一组，剩下两两匹配，共 $n-1$ 组，那么至少有两个出现在同一组中。但这样会产生 $6$ 种特殊情况，考虑使用两数的差来区分，调整顺序可以得到任意特殊情况与其他情况差均不相同的方案。

#### [CF1768F](https://www.luogu.com.cn/problem/CF1768F) Wonderful Jump

如果有 $a_k \le a_i,a_j$，显然 $i \rightarrow k,k \rightarrow j$ 优于 $i \rightarrow j$。即每一步转移区间最小值均在两端。

考虑对每个 $i$ 以 $a_i$ 为最小值向前、向后转移。注意到代价是一次跳过的长度的平方，可以考虑用上界限制转移范围，容易发现长度大于 $\frac{n}{a_i}$ 时一定劣于一步一步跳。

易证复杂度 $O(n \sqrt{n})$。

#### [CF1868D](https://codeforces.com/contest/1868/problem/D) Flower-like Pseudotree

分类讨论。

1. $d_i \ge 2$ 的点有 $n$ 个。
2. $d_i \ge 2$ 的点有 $0$ 个。

以上情况容易构造，剩下的情况考虑把两个 $d_i \ge 3$ 的点组成环（否则显然无解），剩余 $d_i \ge 2$ 的点分别拖两条链。$d_i \ge 2$ 的个数为奇数时需尝试把一个点往尽量浅的位置放，不能则无解。

#### [CF1728F](https://www.luogu.com.cn/problem/CF1728F) Fishermen

相当于 $i$ 和 $a_i$ 倍数连边后跑最大匹配。直接做是 $O(n^4)$ 的，假如每次成功增广后再清空标记数组可以做到 $O(n^3)$。

#### [UOJ883](https://uoj.ac/problem/883) 景点观光

先删掉子树内无关键点的点，猜测最终路线为以某种顺序遍历整棵树，即答案为 $2(n-1)-k$，$k$ 为最多能进行几次跳两步。

考虑 dp，注意到如果从上一个子树出来与进入下一个子树都是跳一步，那么可以考虑合并这两个操作。记 $f_{x,0/1,0/1}$ 表示遍历完 $x$ 子树（包括进入子树与离开子树），第一个操作是否为跳一步，最后一个操作是否为跳一步。$f_{x,0,1}$ 与 $f_{x,1,0}$ 等价，状态可记为 $g_{x,0/1/2}$。

假设已经知道了每个子节点的决策，可贪心地将其拼在一起。观察发现，一般钦定任意一个子节点更换决策对答案的影响不超过 $1$，那么每个子节点选 $g_{i,j}$ 较大的那一个，如有多个最大值选 $j$ 最大的。唯一一种例外是所有子节点决策均为 $0$，自身决策为 $2$ 时，特判即可。

#### [CF429E](https://www.luogu.com.cn/problem/CF429E) Points and Segments

容易想到将染色变为对边定向，即删去覆盖次数为偶数的边后每个连通块跑欧拉回路。

#### [CF1973F](https://codeforces.com/contest/1973/problem/F) Maximum GCD Sum Queries

钦定不交换 $a_1,b_1$，那么两个序列 $\gcd$ 分别是 $a_1,b_1$ 的因数，注意到 $10^8$ 内一个数的因数个数最大不超过 $1000$，考虑以 $(\gcd_1,\gcd_2)$ 为状态。

记 $f_{p_1,p_2}$ 表示最终结果为 $(p_1,p_2)$ 时 $[2,n]$ 中可以满足的 $i$ 的个数（即 $a_i,b_i$ 分别是 $p_1,p_2$ 或 $p_2,p_1$ 的倍数），考虑通过类似高位前缀和的方式转移，只需要在 $f_{\gcd(a_1,a_i),\gcd(b_1,b_i)}$ 与 $f_{\gcd(a_1,b_i),\gcd(b_1,a_i)}$ 处分别 $+1$，在这两个的 $\gcd$ 处 $-1$ 避免重复即可。如果 $f_{i,j} = n-1$ 则表示 $(i,j)$ 合法，而代价可以用相同的方式转移。