---
title: 'CSP-S 2022 游记'
date: 2022-10-30 08:41:54
tags: [游记]
description: ' '
---
## Day 1

进了考场，才发现高新的机房挤得要死，还不隔开坐，跟某东辰国际一样，~~但是某东辰国际至少有挡板，这里瞟代码完全发现不了~~，手都活动不开，键盘盒子还是下沉的，打起来十分奇妙，不过质量感觉比我们机房好很多。

T1 求无向图上过点 $1$ 的最大权五元环，容易想到分成 $1$ 号点左半边和右半边，但是还有保证点互不相同，想了 $10$ 分钟，才想到可以维护前三大值来避免重复。码的时候比较脑车可以写 bfs 的写了个 SPFA。

15:10 开了 T2，策略很显然，如果对面既有正数也有负数，就分别选最接近 $0$ 的正数/负数，否则就选符号相反且绝对值最大的。由于是静态的，可以用 ST 表，但是我怕写挂，还是写了线段树。然后我比较懒，没有分类讨论，直接暴力枚举最小/最大的非负数/负数这四个数组成的情况，反正都有一个 $\log$ 了。

15:40 开 T3，题目非常长，说了半天就是可以对一个数或一个集合中的数同时加 $1$ 或减 $1$，问当前是否每个数都是 $1$。然后我觉得可能是根号分治之类的，由于集合大小总和与点数同级，所以大小比 $\sqrt {N}$ 大的集合最多有 $\sqrt {N}$ 个，但是半个小时都没想出来。然后想到可以把点分成几组（一个点可以在多个组中），我们只需要看每个组的和是否等于它的大小，但是有想了一会，想不到保证正确的分法，但感觉不好卡，实在想不出来就写这个。看了一眼 T4，不会。等到 17:00，仍然想不出来，于是我就按二进制的每一位分组，写出来了。但是我犯了个非常**脑车**的错误，就是在分组判出来没错后线性判，主要是怕 WA，但是当时忘了可能出一堆 `YES` 来卡（心肌梗死的原因）。

17:40 开 T4，好像 $n,m \leq 2000$ 可以跑最短路，但是暴力建边会卡成 $n^2$ 或 $n^3$ 级别的，马上想到分层图，然后就写出来了。虽然还有 $8$ 分可以跑树剖，但是我还没有检查，便放弃了。

18:10 开始检查，首先检查在 Linux 下的编译，好家伙，还真有一个 CE，居然是有个函数忘写 `void` 了。然后检查空间、`ll`，过了遍大样例，还把 T1 拍了一遍。希望没挂分。

## Day 1 晚

出来叶老就问分，T1、T2、T4 加起来 244 分，T3 是真不知道。然后 @sgc 巨佬就来蹭车，还在和 @sxg 巨佬打电话，才发现 T1 脑车了，跑了个 SPFA，不过 @zc 巨佬说他 bfs 没判连通，我 SPFA 初始化的 `inf`，撞大运了。

晚上和 @sgc 巨佬一起吃饭，看到了 [chen_zhe 的帖子](https://www.luogu.com.cn/discuss/520101)。回了寝室之后一直摆烂，结果 22:23 叶老突然就把代码发下来了。luogu 上已经有 T1，T2，T4 的数据，在寝室等了二十分钟不敢交 ~~（期间把 zym 的 T1 交了试试水，结果过了，更慌了）~~。

22:45 终于交了，看起来 T1、T2、T4 都没挂分，刚好 244。T3 交到了 InfOJ 上，发现 T 了，这时才想起来会被卡，然后删了线性判断一交，95 分，当场心肌梗死了。所以我 T3 的瓶颈在于 `put("YES\n")`，然后就心肌梗死了一晚上，和 @zsq 巨佬刷表情，刷了整整 $10$ 分钟，摆烂了。一直到 12 点过才睡。

## Day 2

绵阳又封了，只有待在机房划水。

晚上 @zc 巨佬来机房了，我发现他 T4 过了，问他咋做，他说他抽了一个人的代码交居然过了，得出结论，人均会 T4，就我不会。

luogu 居然一整天都没有 T3 数据。

然后突然发现计蒜客也可以估分，交了一发，T1 居然给我 T 掉了，但在 luogu 上只跑了 500ms，可能是机子太撇了。更震惊的是，T3 给我过了，希望 CCF 的数据也能用脚造。

看了一下 @zlx 巨佬的题解，才发现 T3 脑车了，可以随机赋权值然后看和是否满足。T4 感觉如果多给一些时间应该有点思路。但是完全没有改题的心情，又开始摆烂。

等到快睡觉了才发现 luogu 已经有数据了，交了一发 T3，居然也过了，可能出题人根本不会想到卡我这种复杂度，只有看 rp 了。

## Day 3

下午讲 CSP，听说有人 luogu  T1 的数据 $O(n^4)$ 只 T 一个点，麻了，感觉 T1 各种乱搞都能过。

小图灵也出分了，和 luogu 一样。现在各个网站的分数如下：
luogu ：$100+100+100+44=344$
InfOJ：$100+100+40+44=284$
小图灵：$100+100+100+44=344$
计蒜客：$100+100+100+44=344$
（InfOJ 数据更新后就满了，计蒜客重交也满了）。