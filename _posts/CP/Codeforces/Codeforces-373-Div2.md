---
title: Codeforces 373 Div2
date: 2016-09-27 23:13:57
tags: [Codeforces, 线段树, 矩阵快速幂]
categories: 题记
---

还没有补373Div1的题。。。感觉好难。。。当睡前读物吧。。。

这次Div2的D题出了问题，只有四道了。比的时候出了三道，所以想记的也就那么一道题。。。

[题目链接](http://codeforces.com/contest/719)与[代码链接](https://github.com/2997ms/My_Algorithm/tree/master/Codeforces/373_Div2)



### A. Vitya in the Countryside

> 题意是给出了一个循环的规律，然后给了循环中的某一位置的数字，问能否通过这些信息判断上升还是下降。判断不出来输出-1。

**栽在这上面啦，就算后面发现了也丢失了很多分。。。所谓物极必反，在极值上肯定是可以判断的啊。**



### B. Anatoly and Cockroaches

> 题意是给出了由'r'和‘b’组成的字符串，然后可以由两种操作，1.交换两个位置的字符；2.改变某一位置的字符。问要求最终的字符串是交替的'r''b'，最小操作多少次。

最终要么是rbrbrbr...，要么是brbrbr....

考虑两种情况，然后算r变b的数量x，b变r的数量y，结果min(x,y)+abs(x-y).



### C. Efim and Strange Grade

> 题意是给出了一个长度为$n(n\\leq200000)$的小数，然后最多在小数位进位t次，问最终结果的最大值。要求规范输出。

贪心，找小数位第一个大于等于5的，之后就在这个5之前找连续的大于等于4的，碰到小于4的结束，因为已经进不了位了。注意一些细节。。。

想了好久，写了好久。。。万幸没有fst。

### E. Sasha and Array

> 题意是给出了n个数，m个操作，每个位置上的数字是$f[a\_i\]$，f[x\]代表斐波那契数列的第x项。现在每个操作有两种情况，第一种查询区间和。第二种对区间的每一个值加上f[x\]。

很新颖的题，发现对每一个斐波那契值，是可以矩阵快速幂搞的，那对于区间加上一个值，相当于在矩阵的前面再乘以一个矩阵，所以构建线段树，打lz标记。

这就完了？忽然感觉没啥可说的了，但是为什么做的时候还是觉得难啊。。。