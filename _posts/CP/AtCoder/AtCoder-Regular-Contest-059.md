---
title: AtCoder Regular Contest 059
date: 2016-09-03 23:23:16
tags: [AtCoder,DP]
categories: 题记
---

这一期的后两道DP很好玩。

代码在[这里](https://github.com/2997ms/My_Algorithm/tree/master/AtCoder/Regular_Contest_059)。

### C Be Together

------

题意是给出n个数，它们要统一变成同一个数，代价是差的平方，问最小代价。

由于数量和范围都很小，直接暴力。



### D Unbalanced

------

题意是给出一个字符串，找出这个字符串的子串中是否存在不平衡串，并输出其位置。所谓不平衡串，就是该串中一个字符的数量大于该串长度的一半。

发现最小规模的不平衡串就是在三个以内有超过两个的相同字符，任何不平衡串都要以此为基础。所以一个一个字符暴力就好了。



### E Children and Candies

------

题意是有n个小孩子，c个糖果，将c个糖果分配给n个小孩子，假设孩子得到的糖果数量为a，兴奋值为xi，这个小孩的快乐值为xi^a，幼儿园总的活动值为所有小孩子快乐值的乘积。

现在每一个小孩的兴奋值取值范围为Ai~Bi，问所有情况的幼儿园活动值(…….)的和。

dp[i\][c\]表示i个小孩分配c个糖果的活动值总和，那么仔细分析这个函数，当到i+1的时候，转移的情况为
$$
dp[i+1][c]=\sum\_{j=0}^{c}dp[i][j]\*(\sum\_{x=A[i+1]}^{B[i+1]}x^{c-j})
$$
所以预处理出x的前缀和，直接转移即可。



### F Unhappy Hacking

--------

题意是这个电脑键盘上只有三个键：0，1和delete键，现在按了n次键盘，有一个字符串，问有多少种按键情况能够形成改字符串。

dp[n\][c\]表示按了n次键盘，形成字符串长度为c的方法数，对第n+1次按键盘，可能是delete，转移方程为：
$$
dp[n+1][max(c-1,0)]+=dp[n][c]
$$
如果是按0，转移方程为：
$$
dp[n+1][c+1]+=dp[n][c]
$$
按1同理，在上式基础上再加一遍dp[n\][c\]。

最终因为是固定了0、1，所以除以2^len。