---
title: 'APIO 2023 游记'
date: 2023-05-26 08:09:29
tags: [游记]
description: ' '
---

## Day 0

报道日。

下午三四点到了酒店，纪念品有一个电脑包、一些文件夹和 T 恤。然后排了半个小时的队办入住，和 iee 住一起，再排了十分钟的队打饭，服了。提供的菜种类很多，但只有少数对口味的，但是里面提供了薯条和鸡米花，又排了好久的队去打。

晚上的开幕式没啥看的，倒是有 dzd 的表情包（来源：@lyh）：

![1](/post-images/APIO2023-travels-1.png)

回房间打 generals 和 digdig，打到 11 点睡觉。

## Day 1

由于我和 iee 都看错时间了，早上 7:30 才起，8 点就开始讲课，于是就迟到了，不过去的晚，餐厅也没排队。

今天上午两节字符串，去的时候在讲裸的 AC 自动机，还以为这堂课比较简单，结果后面几道例题直接搞不会了，直接开摆。课间休息时挪到 @lyh 那一脑车人旁边了，预计下节课啥都听不懂，又不敢打游戏，只能坐那水知乎。结果基本全都在讲基本子串结构，听马。

一下课就直接润，但还是排了好久队。吃完饭回房间颓，一看下午的课是 “写一个解释器”，嘶，不如不听。iee 直接不去，但我还是准备下去听一会再说。2 点钟下来坐到最后一排，发现只有一半不到的人，刚准备润，有个人直接说要点名，没来的不发密码条。虽然我觉得不可能不发密码条，但大多数人都下来了。然后又不知道发啥电把我那排全部轰走了（全是 cdqz 的），让新来的人坐。过了一个小时传过来一个点名册，写个名就完，离大谱。

晚上领到了座位号，回房间颓，颓到 11 点睡觉。

## Day 2

测试日。

听说 APIO 的评测机很慢，平均半个小时返回，后面直接变 OI 赛制，他这次声称用了一百台评测机，也不知道会怎么样。

早上 9 点入场试机，评测用的 cms，系统是一个比较奇怪的 Linux，实在不知道码点啥就犯傻码了个快读。

10 点开考，T1 是一个图求，有三种边：把时间 $+w$、除以 $2$、清零，除以 $2$ 最多 $k$ 次。显然可以 $mk \log$ 分层图最短路，一看居然有 $97$ 分，直接开写，剩下 $3$ 分不要了。半个小时码完交上去，看 T2，是求区间中位数的最大个数，想了半天不会。二十分钟后 T1 才测出来，WA 了，Sub0 都 WA 了，只有 $opt=0$ 没 WA。我认为 Sub0 就是样例，但是我显然过了样例，于是脑抽去交了个线上测试。然后写对拍，拍出来了，调一调发现有个地方写挂了，再交。然后开始码 T2 暴力，交了 T2 之后 T1 也出来了，还是 WA，再调，发现可能出现走一次除多遍的情况，再交。T2 的 Sub3 是值单峰，显然可做，直接开码。11:40 的时候 T1 终于返回结果 97 分了，专心搞 T2。

Sub3 写完交，Sub4 是值域 $[1,3]$，显然可以枚举值转化为二维数点，Sub5 是每个值出现次数小于等于 $2$，只需要枚举值判断是否可以为 $2$，两个值中间的必选，看选两边的能不能使这个值为中位数，线段树维护。结果 Sub3 没过，搓对拍，调，再交，然后把 Sub4 和 Sub5 写了交。看 T3，阴间通信套个搓 CPU，Sub1 是 $n=1$，显然只需要乘法器，有 $12$ 分。这道题搓的方法还很阴间，不能套门，还必须输入数据编号小于输出数据编号。还没开始搓，T2 Sub5 没过，又调，交。此时已经只剩半个小时了，搓乘法器，还剩时十分钟搓完了，交上去，此时 T2 还没测完。抓紧读后面的 Sub，感觉还有 $24$ 分比较简单，但感觉码不完。最后 T3 还是没写完。估分 $97 + [47,60] + [0,12] = [144,169]$。

回去一问，好像会 T2 的人很多，还有一大堆 $266$ 的，寄，但应该还是有 $\text{Cu}$。搞了半天不知道在哪看分，在 LA 群上才找到，最终 $169$。

晚上国家队见面，主持人问了很多问题。后面让学生提问，第一个人上去就问：“请问如何评价此次 APIO 2023 比赛”，鼓掌 /gz/gz/gz/gz/gz，回答：“他创造了一种新的赛制”。

## Day 3

今天上午讲多项式，下午杂题，勉强听了一些。

晚上闭幕式，先是选手唱歌，第二首是 《never gonna give you up》，/gz/gz/gz/gz。然后颁线上的奖，没想到线如此低，$\text{Cu}$ $48$，$\text{Ag}$ $111$，$\text{Au}$ $147$。然后又有群人上来跳 《听我说谢谢你》，/gz/gz/gz/gz。然后正式颁奖，$\text{Cu}$ $132$，$\text{Ag}$ $187$，$\text{Au}$ $233$，还是上位 $\text{Cu}$。他们突然在闹 $\text{Au}$ $229$，一看 $\text{Ag}$ 最高 $228$，好像有道理。

开摆。

## Day 4

下午的飞机，可以颓一上午，开摆。

