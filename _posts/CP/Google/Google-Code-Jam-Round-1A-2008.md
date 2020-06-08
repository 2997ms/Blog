---
title: Google Code Jam Round 1A 2008
date: 2016-10-12 22:37:08
tags: [Google,矩阵快速幂]
categories: [题记]
---

[题目链接](https://code.google.com/codejam/contest/32016/dashboard)与[代码链接](https://github.com/2997ms/My_Algorithm/tree/master/Google/GCJ%20Round%201A%202008)

一开始只是特别想记一下C题，后来发现B题也不错。。。

### A Minimum Scalar Product

> 题意是给出了两个$n$维的向量，你可以对这$n$个向量的坐标任意排序，使得他们之间的scalar product(点积$x\_1y\_1+x\_2y\_2+...+x\_ny\_n$)最小。问这个最小点积。

一个正向排序，一个逆向排序。

### B Milkshakes

> 题意是给出了$n$种物品，每种物品可0可1。$m$种顾客，每一个顾客有$T$个要求，每个要求"$X Y$"代表第$X$个物品是$Y$($Y$是0或者1)，其中$Y=1$每个顾客至多一种。要求对每一个顾客至少满足他的一个要求，满足所有顾客的情况下，0的数量最少。如果不可能，输出"IMPOSSIBLE"。

对每一个为0的物品和顾客一个一个检查，如果对一个顾客有一个满足条件的即跳出循环。有为1要求的记录下来，下一次将其改为1。如果都是1，而顾客要求为0导致的都不满足要求的情况，那就直接输出"IMPOSSIBLE"。

```c++
void solve()
{
	memset(state, 0, sizeof(state));
	memset(num, 0, sizeof(num));
	memset(chan, 0, sizeof(chan));

	scanf("%d%d", &n, &m);
	for (int i = 1; i <= m; i++)
	{
		scanf("%d", &num[i]);
		for (int j = 1; j <= num[i]; j++)
		{
			int u, v;
			scanf("%d%d", &u, &v);
			chan[i][j][0] = u;
			chan[i][j][1] = v;
		}
	}
	int fini = 0;
	int imp = 0;
	while (!fini)
	{
		fini = 1;
		for (int i = 1; i <= m; i++)
		{
			int k = -1;
			int j = 1;
			for (j = 1; j <= num[i]; j++)
			{
				if (state[chan[i][j][0]] == chan[i][j][1])
				{
					break;
				}
				if (chan[i][j][1] == 1)
				{
					k = j;
				}
			}
			if (j > num[i])
			{
				if (k == -1)
				{
					fini = 1;
					imp = 1;
					break;
				}
				fini = 0;
				state[chan[i][k][0]] = 1;
			}
		}
	}
	if (imp)
	{
		puts("IMPOSSIBLE");
		return;
	}
	for (int i = 1; i <= n; i++)
	{
		if (i > 1)
		{
			printf(" ");
		}
		printf("%d", state[i]);
	}
	printf("\n");
}
```

### C Numbers

>题意很简单，求$(3+\sqrt{5})^n$的最后三个整数部分的值。

这个是一个很经典的问题了，特意来记录一下。

设$a=3+\sqrt{5}$，$b=3-\sqrt{5}$，$x\_n=a^n+b^n$。

会发现得到的$x\_n$是一个整数，另外由于$b\_n<1$，所以得到的$x\_n$是大于结果的第一个整数。所以现在考虑求这个$x_n$。

然后对于$x^{n+1}$来说，有$(3+\sqrt{5})(a\_n+b\_n\*\sqrt{5})=(3a\_n+5b\_n)+(3b\_n+a\_n)\*\sqrt{5}$，那么可以得到$a\_{n+1}=3a\_n+5b\_n$，$b\_{n+1}=3b\_n+a\_n$。

这样可以化成矩阵的形式：
$$
\begin{pmatrix}
   a\_{n} \\\
   b\_{n+1}
  \end{pmatrix}=A\begin{pmatrix}
   a\_{n-1} \\\
   b\_{n-1}
  \end{pmatrix}=A\_{n}\begin{pmatrix}
   a\_{0} \\\
   b\_{0}
  \end{pmatrix}
$$
其中
$$
A=\begin{pmatrix}
   3&5 \\\
   1&3
  \end{pmatrix}
$$
因为$a\_0=1$,可以得到$(a\_0,b\_0)=(1,0)$

~~太困了。。。明天接着填。。。。~~

这样就可以矩阵快速幂啦~

