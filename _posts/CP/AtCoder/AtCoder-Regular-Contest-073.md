---
title: AtCoder Regular Contest 073
date: 2017-05-06 10:34:44
tags: [AtCoder]
categories: 题记
---

做了一下ARC073，又是好久没做题了。。。AtCoder的题怎么总是感觉不可做啊，每次解法都好神啊。。。

<!--more-->

### C - Sentou

```c++
int n,t;
int val[maxn];
 
void solve()
{
	ll res = 0;
	sa(n),sa(t);
	sa(val[1]);
 
	repp(i,2,n)
	{
		sa(val[i]);
		if(val[i]>=val[i-1]+t)res+=t;
		else
		{
			res+=val[i]-val[i-1];
		}
	}
	res+=t;
	printf("%lld\n", res);
}
```



### D - Simple Knapsack

>题意是有$N(N<=100)$种物品，每种物品重量$w(w<=10^9)$，价值$v(v<=10^9)$。你现在有可以承重$W$的背包，问最大价值。然后多了一个条件，$N$种物品重量之间相差不超过$3$。

可以DP的，背包状态最多$3\*N$的。

然后可以对每种重量(一共就4种)做贪心的，复杂度最坏是($\frac{N^4}{4}$)。

```C++
int N, W;
int w[maxn], v[maxn];
vector<ll>val[10];
 
void solve()
{
	ll res = 0;
	sa(N), sa(W);
	ll ind;
	repp(i, 1, N)
	{
		sa(w[i]), sa(v[i]);
		if (i == 1)ind = w[i];
		val[w[i] - w[1]].push_back(v[i]);
	}
	rep(i, 0, 4)
	{
		sort(val[i].begin(), val[i].end(), greater<ll>());
	}
	ll s1 = 0, s2 = 0, s3 = 0, s4 = 0;
	ll ans = 0;
	for (int i = 0; i <= val[0].size(); )
	{
		s2 = 0;
		for (int j = 0; j <= val[1].size(); )
		{
			s3 = 0;
			for (int k = 0; k <= val[2].size();)
			{
				s4 = 0;
				for (int h = 0; h <= val[3].size();)
				{
					ll sum = 1LL * i*(ind)+1LL * j*(ind + 1) + 1LL * k*(ind + 2) + 1LL * h*(ind + 3);
					if (sum <= W)
					{
						ans = max(ans, s1 + s2 + s3 + s4);
					}
					h++;
					if (h >= 1&&h<=val[3].size())s4 += val[3][h - 1];
				}
				k++;
				if (k >= 1 && k <= val[2].size())s3 += val[2][k - 1];
			}
			j++;
			if (j >= 1 && j <= val[1].size())s2 += val[1][j - 1];
		}
		i++;
		if (i >= 1&&i<=val[0].size())s1 += val[0][i - 1];
	}
	printf("%lld\n", ans);
}
```



### E - Ball Coloring

> 题意是给出$2\*N$个球，两两一组，一组内的球一个染成红色，一个染成蓝色，每个球有标号，问所有染成红色的球中标号的最大值最小值之差与蓝色的球标号的最大值最小值之差乘积最小，即$(R\_{max}-R\_{min})\*(B\_{max}-B\_{min})$的值最小。
>
> 这个E题要怎么想出来嘛，太难了啊不会做啊。。。每次都去看题解挫败感很大啊。。。

这$2\*N$的球会有$MAX、MIN$，那么最终结果会有两种情况，

1.$R\_{min}=MIN，B\_{max}=MAX$，那么这种情况就很简单了，让每一对较小的给染成红球，较大的给蓝球。

2.$R\_{min}=MIN，R\_{max}=MAX$，将较小的排序，然后不断换成大的，利用单调性，发现这样就可以遍历所有情况了。这种做法给跪了。。。

```C++
int n;
pair<ll,ll>val[maxn];
multiset<ll>v1,v2;
 
void solve()
{
	sa(n);
	if(n==1)
	{
		puts("0");
		return;
	}
	repp(i,1,n)
	{
		int x,y;
		sa(x),sa(y);
		if(x>y)swap(x,y);//x<=y
		val[i].first = x;
		val[i].second = y;
		v1.insert(x);
		v2.insert(y);
	}
	sort(val+1,val+n+1);
	ll ans = 1LL*(*v1.rbegin()-*v1.begin())*(*v2.rbegin()-*v2.begin());
	repp(i,1,n)
	{
		v1.erase(v1.find(val[i].first));
		v2.erase(v2.find(val[i].second));
 
		v1.insert(val[i].second);
		v2.insert(val[i].first);
		ans = min(ans,1LL*(*v1.rbegin()-*v1.begin())*(*v2.rbegin()-*v2.begin()));
	}
	printf("%lld\n", ans);
}
```



### F - Many Moves

> 两个机器人，起始位置分别为$A、B$，然后有相关的命令，$x\_{i}$，要求必须有一个机器人在位置$x\_{i}$，然后每一次机器人到$x\_{i}$会有一个移动距离，问执行所有命令之后，移动距离最少为多少。

F题有一道很类似的题目，在hackerrank上，[题目链接](https://www.hackerrank.com/contests/w19/challenges/two-robots)。

先从最暴力的DP考虑，$dp[i\][x\][y\]$表示到第i次命令，一个球在位置$x$，一个球在位置$y$，然后转移。复杂度$O(Q\*N^2)$。

然后考虑在执行$x[i\]$命令时，一个球位置一定在$x[i-1\]$，所以枚举另一个球位置就好了，这也就是Hackerrank的那道题。$O(Q\*N)$。

复杂度还是不够，假设你已知$dp[i-1\][k]$，考虑就两种转移方式到$dp[i\][k]$：

1.从$x[i-1\]$转移过来，对所有的$dp[i\][k]$都加上$abs(x[i\]-x[i-1\])$。

2.$dp[i][x[i-1]]=min(dp[i-1][j]+abs(j-x[i])) {for \ j \ in \ range(1,N)}$，然后把$abs$脱下，会发现是两棵线段树，维护一下$dp[i-1\][j\]+j$和$dp[i-1\][j\]-j$就好了。

```C++
ll seq[maxn];
 
struct Segtree
{
	struct Node
	{
		ll val, add;
	};
	Node tree[maxn * 4];
	void modify_add_Node(int node, ll val)
	{
		tree[node].val += val;
		tree[node].add += val;
	}
	void pushup(int node)
	{
		tree[node].val = min(tree[node << 1].val, tree[node << 1 | 1].val);
	}
	void pushdown(int node)
	{
		if (tree[node].add)
		{
			modify_add_Node(node << 1, tree[node].add);
			modify_add_Node(node << 1 | 1, tree[node].add);
			tree[node].add = 0;
		}
	}
	void build(int l, int r, int node)
	{
		if (l == r)
		{
			tree[node].val = seq[l];
			tree[node].add = 0;
			return;
		}
		int mid = (l + r) >> 1;
		build(l, mid, node << 1);
		build(mid + 1, r, node << 1 | 1);
		pushup(node);
	}
	void modify_add(int L, int R, int l, int r, int node, int val)
	{
		if (r < L || R < l) return;
		if (L <= l&&r <= R)
		{
			modify_add_Node(node, val);
			return;
		}
		pushdown(node);
		int mid = (l + r) >> 1;
		modify_add(L, R, l, mid, node << 1, val);
		modify_add(L, R, mid + 1, r, node << 1 | 1, val);
		pushup(node);
	}
	void modify_min(int pos, int l, int r, int node, ll val)
	{
		if (l == r)
		{
			tree[node].val = min(tree[node].val, val);
			return;
		}
		pushdown(node);
		int mid = (l + r) >> 1;
		if (pos <= mid) modify_min(pos, l, mid, node << 1, val);
		else modify_min(pos, mid + 1, r, node << 1 | 1, val);
		pushup(node);
	}
	ll query_min(int L, int R, int l, int r, int node)
	{
		if (r<L || R<l) return INF;
		if (L <= l&&r <= R) return tree[node].val;
		pushdown(node);
		int mid = (l + r) >> 1;
		return min(query_min(L, R, l, mid, node << 1), query_min(L, R, mid + 1, r, node << 1 | 1));
	}
	ll dfs(int l, int r, int node)
	{
		if (l == r) return tree[node].val + l;
		pushdown(node);
		int mid = (l + r) >> 1;
		return min(dfs(l, mid, node << 1), dfs(mid + 1, r, node << 1 | 1));
	}
}s,t;
 
int N, Q, A, B;
int x[maxn];
 
void solve()
{
	sa(N), sa(Q), sa(A), sa(B);
	repp(i, 1, Q)
	{
		sa(x[i]);
	}
	repp(i, 1, N)
	{
		seq[i] = INF;
	}
	seq[B] = -B;
	s.build(1, N, 1);
 
	seq[B] = B;
	t.build(1, N, 1);
 
	x[0] = A;
	repp(i, 1, Q)
	{
		ll tmp = min(s.query_min(1, x[i], 1, N, 1) + x[i], t.query_min(x[i], N, 1, N, 1) - x[i]);
 
		s.modify_add(1, N, 1, N, 1, abs(x[i] - x[i - 1]));
		t.modify_add(1, N, 1, N, 1, abs(x[i] - x[i - 1]));
 
		s.modify_min(x[i - 1], 1, N, 1, tmp - x[i - 1]);
		t.modify_min(x[i - 1], 1, N, 1, tmp + x[i - 1]);
	}
	ll ans = s.dfs(1, N, 1);
 
	printf("%lld\n", ans);
}
```



