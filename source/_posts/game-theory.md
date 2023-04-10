---
title: '博弈论'
date: 2023-04-10 10:03:55
tags: [博弈论]
description: ' '
---

## 公平组合游戏

公平组合游戏的定义如下：

- 游戏有两个人参与，二者轮流做出决策，双方均知道游戏的完整信息；

- 任意一个游戏者在某一确定状态可以作出的决策集合只与当前的状态有关，而与游戏者无关；

- 游戏中的同一个状态不可能多次抵达，游戏以玩家无法行动为结束，且游戏一定会在有限步后以非平局结束。

### 博弈图和状态

如果将每个状态视为一个节点，再从每个状态向它的后继状态连边，我们就可以得到一个博弈状态图。一个棋子位于初始状态，两人轮流移动棋子，每次移动一步，不能移动者败。

定义必胜状态为先手必胜的状态，必败状态为先手必败的状态。

如果一个状态没有后继状态，那么棋子到达这里就无法移动，此时当前的先手必败，也就是必败状态。

如果一个状态的后继状态中有一个必败状态，那么先手移动到这个必败状态就可以保证后手必败，当前状态就是必胜状态。

如果一个状态的后继状态全部是必胜状态，无论先手怎么移动后手都必胜，当前状态就是必败状态。

根据定义可得，一个公平组合游戏的博弈图是一个 DAG，就可以一次拓扑排序求出每个状态必胜还是必败。

### SG 函数

在一个 DAG 中，一个棋子位于初始状态，两人轮流移动棋子，每次移动一步，不能移动者败。这样的游戏叫做有向图游戏。事实上，因为公平组合游戏的博弈图是一个 DAG，所以公平组合游戏可以转化为有向图游戏。

定义 $\text{mex}$ 函数的值为不属于 $S$ 的最小非负整数，即：
$$
\text{mex}(S) = \min\{x\} (x \notin S,x \in N)
$$
定义 $\text{SG}$ 函数为其后继状态的 $\text{SG}$ 的异或和，即对于一个状态 $x$，设其后继状态为 $y_1,y_2,\dots,y_k$：
$$
\text{SG}(x) = \text{mex}\{\text{SG}(y_1),\text{SG}(y_2),\dots,\text{SG}(y_k)\}
$$
一个有向图游戏的 $\text{SG}$ 值为其初始状态的 $\text{SG}$ 值。

对于一个包含 $n$ 个有向图游戏的组合游戏，这个游戏的 $\text{SG}$ 值为这 $n$ 个有向图游戏的 $\text{SG}$ 的异或和。设第 $i$ 个有向图游戏的初始状态为 $s_i$，这个游戏的 $\text{SG}$ 值就是 $\text{SG}(s_1) \oplus \text{SG}(s_2) \oplus \dots \oplus \text{SG}(s_n)$。

一个游戏是先手必胜的，当且仅当这个游戏的 $\text{SG}$ 值不为 $0$。这个定理被称为 SG 定理。

#### 证明

我们假设当前状态的后继状态都满足 SG 定理，需要证明当前状态也满足 SG 定理。

显然没有后继状态的状态 $\text{SG}$ 为 $0$，且为必败状态。

如果当前状态 $\text{SG}$ 值为 $0$，不管怎么移动 $\text{SG}$ 值都要改变，它所有的后继状态 $\text{SG}$ 均不为 。

如果当前状态 $\text{SG}$ 值 $k$ 不为 $0$，设 $k$ 二进制下最高位为 $d$，显然一定有一个单一状态的 $\text{SG}$ 值 $\text{SG}_i$ 第 $d$ 位为 $1$，异或后显然 $\text{SG}_i$ 变小，一定存在一个后继状态的 $\text{SG}_j = \text{SG}_i \oplus k$。所以先手一定可以移动到一个 $\text{SG}$ 值为 $0$ 的状态。

许多的公平组合游戏的结论都可以用这种方式证明，只需要证明一下 $3$ 点：

1. 没有后继状态的状态为必败状态。
2. 任何一个必胜状态，其后继状态全部是必败状态。
3. 任何一个必败状态，都可以转移到一个必胜状态。

### Nim 游戏

$n$ 堆物品，每堆有 $a_i$ 个，两个玩家轮流取走任意一堆的任意个物品，但不能不取，取走最后一个物品的人获胜。

显然一堆 $x$ 个物品取一次后可以变成 $[0,x-1]$ 个物品，建出博弈图后是 DAG，也就是每一堆都是一个有向图游戏，整个 Nim 游戏就包含 $n$ 个有向图游戏的一个组合游戏。显然一个有 $x$ 个物品的状态 $\text{SG}$ 值为 $x$，整个游戏的 $\text{SG}$ 值为 $a_1 \oplus a_2 \oplus \dots \oplus a_n$。当且仅当物品数量异或和不为 $0$ 时先手必胜。

#### 反 Nim 游戏

$n$ 堆物品，每堆有 $a_i$ 个，两个玩家轮流取走任意一堆的任意个物品，但不能不取，取走最后一个物品的人失败。

虽然看起来不符合公平组合游戏的条件，但是只需要每个结束状态后再接一个状态即可。

结论：

1. 所有堆的物品数都为 $1$ 且物品数的异或和为 $0$。

2. 存在堆的物品数大于 $1$ 且物品数的异或和不为 $0$。

#### k-Nim 游戏

$n$ 堆物品，每堆有 $a_i$ 个，两个玩家轮流取走任意 $k$ 堆的任意个物品，但每一堆至少取 $1$ 个，取走最后一个物品的人获胜。

结论：当在二进制下，对于每一位，$a_i$ 在这一位为 $1$ 的个数都 $\bmod (k+1) = 0$ 时先手必败，否则先手必胜。

#### 阶梯 Nim 游戏

$n$ 堆物品，每堆有 $a_i$ 个，两个玩家轮流把任意一堆的任意个物品移动到左边一堆，但不能不移动，无法移动的人失败。

结论：对奇数层的石子个数进行 Nim 博弈，算出异或和的值，若非 $0$，先手必胜，否则先手必败。

#### Muti-Nim 游戏

$n$ 堆物品，每堆有 $a_i$ 个，两个玩家轮流取走任意一堆的任意个物品，但不能不取，或者把一堆物品分成两堆数量不为 $0$ 的物品，无法操作的人失败。

实际上每次分裂都是把一个子游戏分成了两个子游戏，$\text{SG}$ 值也就是两个子游戏的异或和。

结论：

{% raw %}
$$
\text{SG}(x)=
\left\{
\begin{aligned}
x-1 \ \ \ \ x \equiv 0 \pmod 4\\
x \ \ \ \ x \equiv 1,2 \pmod 4\\
x+1 \ \ \ \ x \equiv 3 \pmod 4
\end{aligned}
\right.
$$
{% endraw %}