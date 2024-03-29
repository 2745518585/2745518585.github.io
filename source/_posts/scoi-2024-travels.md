---
title: 'SCOI 2024 游记'
date: 2024-2-28 14:25:10
tags: [游记]
description: ' '
---

省流：@zsq Day2 切穿 $218$ 分成功登顶。

## Day $-6 \sim -4$

被抓去听电子科大的省选集训，虽然不想去听，但是家长很积极，早就把钱交了。其实听电子科大师傅讲一哈重点也不是不行，主要是我想多打一哈板子，我现在还不能保证我场上能打出半平面交。

结果前两天 sjx 讲 dp 和计数，第三天 ljy 讲 ds，还从 ljy 那知道了后面是 lyh、kot 和一套他们三个组的题。然后大家才发现被电子科大诈骗了，因为是 ygg 声称的有电子科大师傅。然后第二天大多数人都润了。

## Day $-3 \sim 0$

回到机房打板子，当然同时高强度水知乎，不过有一天 ygg 直接来机房坐了一上午有点哈人。

## Day $1$

本来还以为和去年那个批机子一样（键盘上有关机键），结果更批，直接上了难以想象的 10300，装的 win10 可能因为带不动 win11。然后还没开考就有人电脑黑了，监考声称是考生自己踢掉的，吓得我整场腿都是缩起的。

开考后听到 @suk 报告说打开 win 下的 vscode 就卡死了，然后监考来了句 《电脑上有 $\neq$ 你能用》，干沉默。

开题，T1 串串题，T2 计算几何，T3 不会。（要素齐全）

先开 T1，T1 大概就分 5 种情况讨论，其中一种需要暴力跳 fail 树，但跳的次数不超过串长和。不过感觉不是很好写，最后写了 4k 调了半个小时才搞完，写完 1h30min。

开 T3 打暴力，感觉 $m^n$ 枚举能冲 $n=10,m=7$ 的 $60$ 分，想了半天如何 $O(n)$ 求答案，最后还是搞了个 $O(n^2)$，然后记忆化了一些东西。实际跑极限数据近二十秒，只能说 i3 和 Ryzen 7000 还是有很大的差距。写完 2h30min

T2 感觉只会 $2^n n$，共圆和共线的情况感觉可以 $n^3$，不行也能暴力跑 $n^4 \log n$，先把 $20$ 分暴力写出来，此时已经只剩半个小时了。然后想到可能可以直接拆矩形拆成很多块，但是当时不知道抽啥风觉得块数是指数级的。开始搞圆的性质，写了一车我觉得很对的东西但是过不了样例，遂开摆。

最后一分钟叉掉虚拟机的时候整个卡死了，直到考试结束都没好，只能盯到电脑看。幸好最后还是转出来了，服了。

估分 $100 + 20 + [30,60]$。

由于是 SC 自己测，三点过就出分了，T3 有 $55$，不过排名比我期望的高，大家好像都是一百五六，只有 @zlx $245$ 预定队长。

## Day $2$

开题，T1 计数，T2 不知道算啥，T3 构造。

写 T1，场上只想到了 $n \sum |S|$，不过感觉 i3 上跑的不慢所以感觉能冲就没管了。写完 1h30min。

T2 容易发现 $ans \leq 2$，判断 $0$ 是容易的，只需要判断 $1$，问题转换为不断加边维护 $S$ 到 $T$ 的割边，感觉只需要提一条连通的路径出来然后不断删即可。写了 2h 多才写出来，总共 6k，然后开始疯狂调，最后二十分钟还没调出来，于是先去打了 T3 的 $\min(m,n) = 1$，回来继续调，最后还是没调出来，暴力也没写，希望这玩意能有点分。

估分 $[65,100] + 0 + 10$，输麻了。

被家长拉去和师傅吃饭，吃到一半就出分了，$65 + 10 + 10$，只能说没掉出队。@zsq $218$ 成功 D2 标准分，yl 翻掉 @zlx 成为队长。

## Day $3 \sim$

摆。

后面在机房调了一哈 D2T2，一下就发现了一个弱智错误，又调了十几分钟发现了一个细节没处理好，然后就直接过了。这时候应该祭出 @Feyn 的那张图：

![我他喵的在淦鸟吗]()

~~找不到了~~

评价这次 SCOI，只能说是抽象。题倒是不出意料，但是这考点设备啥的属实抽象麻了（后面还看到说有无线网卡，才想起来确实有个师傅过来把我的机箱翻出来干了啥又塞回去，[link](https://www.luogu.com.cn/discuss/782820)）。考完之后的申诉必须半个小时内提交，还不下发数据，建议学习 CCF 申诉缴费。包括之前把我们骗去集训，正常人都不会去第二次，电子科大还能恰几年钱我不好说。