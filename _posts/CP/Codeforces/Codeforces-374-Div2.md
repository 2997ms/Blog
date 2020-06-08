---
title: Codeforces 374 Div2
date: 2016-10-06 18:05:07
tags: [Codeforces, DP, 优先队列]
categories: [题记]
---

这一轮就过了A和B，C题看见直接懵了，不会做。xjb写了一个宽搜，然后又被叉掉了。D题有思路又没有写完。

[题目链接](http://codeforces.com/contest/721)与[代码链接](https://github.com/2997ms/My_Algorithm/tree/master/Codeforces/374_Div2)

### A One-dimensional Japanese Crossword

> 找成段的b的区间。

### B Passwords

> 密码长度为k，从最小的长度开始试验，随机在该长度的字符串里面xjb找，问最小代价与最大代价。

将所有字符按照长度排序，算小于k的与小于等于k的。

### *C Journey

> 题意是给出每两点之间要通过的时间，给出起点是$1$，终点是$n$。问在给定T时间的范围内，能走过的最多的点数。(点数$n \leq 5000 $，边数$m \leq 5000$ 时间$T\leq 10^9$)

现在来考虑这道题，$dp[x\][y\]$表示搜到$x$点时，经过$y$个点所用的最短时间。然后这个深搜搜到第$n$个点为止。在搜的过程中记录前一个节点，代码如下：

```c++
void cal(int x)
{
	if (vis[x])
		return;
	vis[x] = 1;
	if (x == n)
	{
		dp[x][1] = 0;
	}
	for (int i = head[x]; i != -1; i = edge[i].next)
	{
		int nxt = edge[i].to;
		int t = edge[i].tim;
		cal(nxt);
		for (int j = 1; j <= n; j++)
		{
			if (dp[x][j] > t + dp[nxt][j - 1])
			{
				dp[x][j] = t + dp[nxt][j - 1];
				pre[x][j] = nxt;
			}
		}
	}
}
```

就这种问题肯定是要同时记录时间和数量的，考虑将其中一个作为数组下标，另一个作为变量。

### *D Maxim and Array

> 给出$n$个数，可以对其中任意一个数做$+x$或者$-x$的操作，现在最多能够操作$k$次，问使得这$n$个数相乘的最小的情况下，这$n$个数分别是多少。

首先判断0的数量$x$，$x>k$，只能是$0$。

> 之后判断正负号，负号不管。正号的话，考虑把正的最小的变到负的，或者把负的最大的变成正的。(后一种情况要考虑到)

做完这些操作之后，会发现问题变成使得它们相乘的绝对值最大，可以推出来，有$n$个数，可以对任意一个操作的时候，一定是选绝对值最小的那个数$+x$。那么这样的话就维护队列中的最小值不断$+x$就可以了。

自己打得时候完全懵逼，拿一个线段树去维护最小值也是。。。用优先队列啊。。。



### *E Road to Home

> 题意是给出了$L$范围内的$n$段区间，小朋友在这$n$段区间可以唱歌，每次唱歌走过的路程为$p$，当然也可以不唱。唱完歌走出一个区间至少要隔$t$长度才可以再唱，问最多能够唱几次。

考虑每一个区间，$dp[x\]$表示到第$x$区间的右端点时最多唱过的歌数。$en[x\]$记录其位置。如果有相同数量的情况，记录其最左边的位置。

这个题巧妙在于每一次只需记录利用该区间之后的最大数量和最左位置就好了，而不用每一次从1开始转移，原因[这篇博客](http://www.cnblogs.com/sagitta/p/5925785.html)说得很清楚了。

> 你如果认真读过转移方程的话会发现 转移的时候是每次先休息再连续唱几遍歌 （先一段休息 再一段唱歌） 我们考虑唱歌段 如果唱歌段能继续延长一个周期 那么如果此时延长了肯定会使得从起点到当前区间的答案更优 **而起点到每个区间的最优答案都是从起点到之前某个区间的最优答案转移过来的 如果某一区间少唱一首歌 那么空余出来的时间在这之后运气好也只能用来唱一首歌** 所以只要按这种贪心思路 维护从起点到当前区间的最优解 最后得到的便是从起点到终点的最优解。

这个题$O(n)$的解法真的很巧妙啊，比题解的那个根据p的范围分类讨论各种情况的好多了。。。