---
title: Codeforces 375 Div2
date: 2016-10-06 20:25:18
tags: [Codeforces]
categories: [题记]
---



[题目链接](http://codeforces.com/contest/723)与[代码链接](https://github.com/2997ms/My_Algorithm/tree/master/Codeforces/375_Div2)

A,B,D都很简单很简单了，这场C题题意读偏了一些+没有判断数组的范围，GG。E和F两个构造，都没敢想。。。。E题一直在考虑网络流解，但是想不到怎么建图。

### A The New Year: Meeting Friends

> 三个点走到同一个点的最短距离。

都减去中位数的绝对值的和。

### B Text Document Analysis

> 给出一个字符串序列，问在括号里面的单词数量和在括号外面的单词最大长度。

维护当前的状态，在括号内还是在括号外，对每一个状态记录相应的结果。



### C  Polycarp at the Radio

> 题意很绕，很烦。。。
>
> 题意是给出$n$个数，$a_i$表示第i首歌曲由$a_i$这支乐队演奏。现在这个人希望重新分配曲目，使得在所有情况中，$1$到$m$这些乐队最少演奏曲目的那支乐队演奏得最多。

最终的最小值肯定是$n/m$。

没有判断好范围啊，就是xjb写的重写分配。对于每一种$m$，当数量大于$n/m+n%m$的时候，就给数量少的。如果当前的1到m的乐队有数量不够的，从大于m的值里面或者数量多的里面拿。复杂度$O(mn)$。



### D Lakes in Berland

> 题意是给出$n\*m$的格子，'*'代表陆地，'.'代表水。lake是指被陆地包围的格子连通块。不被陆地包围在边界上的或者连接着便捷的水属于海洋。现在要求把lake数量变到$k$。问最小改变多少个格子。

纯粹就是xjb深搜，找出面积最小的$n-k$个，算和。



### *E One-Way Reform 

> 给出$n$个点，$m$个边，现在这些边是没有方向的，要求你对每一条边定一个方向，使得出边=入边的点的数量最多。

对每一个奇度点，连到$n+1$这个点上。然后跑欧拉回路，不断删边。

```C++
int n, m;
set<int>ed[maxn];
int out[maxn], in[maxn];
vector<pair<int, int>>res;

void dfs(int x)
{
	while (ed[x].size())
	{
		auto it = ed[x].begin();
		int p = *it;
		res.push_back(mp(x, *it));
		ed[x].erase(it);
		ed[p].erase(x);
		dfs(p);
	}
}

void solve()
{
	scanf("%d%d", &n, &m);
	memset(in, 0, sizeof(in));
	memset(out, 0, sizeof(out));
	for (int i = 1; i <= n + 1; i++)
	{
		ed[i].clear();
	}
	for (int i = 1; i <= m; i++)
	{
		int x, y;
		scanf("%d%d", &x, &y);
		out[x]++;
		in[y]++;
		ed[x].insert(y);
		ed[y].insert(x);
	}
	int ans = 0;
	for (int i = 1; i <= n; i++)
	{
		if ((out[i] + in[i]) % 2)
		{
			ed[i].insert(n + 1);
			ed[n + 1].insert(i);
		}
		else
		{
			ans++;
		}
	}
	res.clear();
	for (int i = 1; i <= n; i++)
	{
		dfs(i);
	}
	printf("%d\n", ans);
	for (int i = 0; i < res.size(); i++)
	{
		if (res[i].first == n + 1 || res[i].second == n + 1)continue;		
		printf("%d %d\n", res[i].first, res[i].second);		
	}
}
```

建网络流也是可以的，我始终隐约觉得遇到过这题，结果看了[这篇博客](http://blog.csdn.net/snowy_smile/article/details/52732805)之后更加确信自己做过了(这种网络流的题目还是很常见的，下一次碰到类似的争取想出来~)。



### *F st-Spanning Tree

> 给出$n$个点，$m$条边，要求构建一棵树，使得给定的两个点$s$,$t$的度数不超过$d_s$,$d_t$。

首先先无脑将不包含$s$，$t$的边放进去。

之后会发现是一个个的连通块，首先找有没有连通块连接着$s$和$t$的，没有的话，那么肯定有$s$，$t$这条边，连上。之后连$s$的连$s$，连$t$的连$t$。

要是有连通块连接着$s$和$t$的，连上之后，能连$s$的连$s$，能连$t$的连$t$。