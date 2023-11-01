---
title: 'CSP-S 2023 游记'
date: 2023-10-23 11:41:54
tags: [游记]
description: ' '
---

## $\sim \text{Day} 0$

出准考证号才发现，大家都是一百多两百多，就我是 552，自闭。不过 zpl 更自闭：祂在 myns 考试，CCF 日常发电。

## $\text{Day} 1$

颓了一上午加一中午。

一点四十左右到高新门口，还比较早，大多数人都还没来，不过老远就看到 ygg 在学校里面和别的师傅谈笑风生。看到 zsq syy 等人居然带了咖啡，一看就是要启动，就我只带了一瓶香槟（矿泉水）。

进考场之后才发现一个严重的问题：我这个机房居然是 win10 老机子？？？一看 CPU 10700，还好，不是 4 代 i5 之类的。罚坐 20min。

启动！T1 哈麻皮，T2 感觉很能做，T3 出题人****，T4 暂时没思路。

T1 在 15min 时写完。

T2 猜一个 $f_i=f_{i-g_i}+1$，$f_i$ 是以 $i$ 结尾的答案，$g_i$ 是 $i$ 结尾最短偶回文串长度，证了一会发现是对的。求 $g$ 数组用了二十分钟胡了好几种 $O(n)$ 做法，但都发现是假的。然后尝试 $O(n \left| \sum \right|)$，用 $h_{i,j}$ 表示以 $i$ 结尾再跟一个字符 $j$ 的最短偶回文串长度，显然就可以转移了。写完大概 50min。

启动 T3，数据范围很小，直接上 `std::map` 和 `std::string`。一个小时差不多写完，没过第二个样例，才发现读错题了，问题不大，改改就过了，这时才看到最后的形式化题面，我谢谢你 CCF。写完 2h 多一点。

T4 开题就发现如果确定了结束时间，那么每棵树所需的生长时间是可知的。二分结束时间，每棵树的最晚种植时间为 $f_u$，由于需要先种父亲再种儿子，所以 $f_u=\min(f_u,f_v+1)$，双 $\log$ 开个优先队列每次取最早的即可，单 $\log$ 想了一会没想出来，先去想怎么求 $f$。每棵树所需的生长时间需要解二次方程得到，但是搓了半个小时怎么都搞不对，直接开摆写二分加 `__int128_t`，一遍过大样例，最后一个大样例需要跑 $0.7\text{s}$。前面一半是双 $\log$，后面的单 $\log$ 也懒得想了，CCF 的评测机 8700K 要比本机多一个 K，可以钦定不会被卡，而且双 $\log$ 和暴力一个分也过于抽象了。

写完还剩半个小时，搓了个快读，拍了一下 T2。本来想搓个字符画但是搓出来的 $2$ 怎么看怎么不对，只能直接写上去：~~（顺便广告：）~~

{% contentbox type:example title:code %}
```cpp
// from 2745518585
// Ad: Orita: Useful OI Tools: https://github.com/2745518585/Orita
```
{% endcontentbox %}

这次题相当简单，感觉大家都能 AK。出来先遇到 Feyn，问 T4 单 $\log$，好像祂也不会，问了一圈感觉没有写单 $\log$ 的，感觉稳了。结果出来被告知要去一起吃饭，ygg 也在，要瓜起。

听到 T2 原题 CF1223F，T4 好像是哪个学校的模拟赛的题，CCF 就出了个 T1 T3？不好评价。

由于 ygg 在谈笑风生，到 10 点都没人发 src，找高一的师傅要了一份，luogu 上 T1 T2 都过了，yundou 上全过了。

颓。

## $\text{Day} 2 \sim$

> [关于部分平台私自发布CSP-J/S 2023认证结果的公告](https://www.noi.cn/xw/2023-10-23/796608.shtml)

樂，CCF 恼羞成怒。

过了两天又有小周老师新文。

> [23年提高组题解](https://mp.weixin.qq.com/s?__biz=MzIwMzcxMTY4OQ==&mid=2247485279&idx=1&sn=8e946a1dbf097603d7fe999f98d52a88&chksm=96ca72f9a1bdfbef6db370b669792513234dadd960198d8bff451bb8b0cd35bb001cb5ef6e69&mpshare=1&scene=23&srcid=1024wxs9ddTvySY1xAZQz7gO&sharer_shareinfo=2a2f8c1b00d1aaa6ddaa9b3d5b5efffb&sharer_shareinfo_first=2a2f8c1b00d1aaa6ddaa9b3d5b5efffb#rd)

> 5- 退役战，APIO获得银牌，排名100，成绩远超曾经上海赛区NOIP排名第一的同学。

> 【小周老师注：一定要赞美CCF啊同学们！】

> 但我并不认为CCF以后会继续朝着OOP或者软件工程靠近，毕竟，NOIP存在的意义，是为了选出4名国手参加IOI，算法优秀就行了，其他的回头再说。

樂

至于这个 “谦哥”，这简历详细的，属于是无效打码了。甚至高二没进省队还在打 APIO，不好评价。

![1](/post-images/csp-s-2023-travels-1.png?300x)

😭😭😭晚上被 ygg 抓到 UB 了，T2 摸了数组下标 $-1$，在 dev 上要 RE😭😭😭😭😭😭😭😭😭😭😭😭😭😭😭😭😭😭😭😭😭😭😭。有的同学喜欢写 UB，评价是不如原神。

以后测试必加 `-fsanitize=undefined`。

30 号晚上查分，传统艺能 `https://cspsjtest.noi.cn/page/compStudent/resultStudent.php?compID=83`。没挂。

不知道有没有群 u 冲申诉加 100 分的。