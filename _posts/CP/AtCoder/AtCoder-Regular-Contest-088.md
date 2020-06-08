---
title: AtCoder Regular Contest 088
date: 2018-01-26 18:19:04
tags: [AtCoder, DP, 二分]
categories: [题记]
---

这期的D、E、F题都很有趣。总结下来，E题做的时候没想法。F题琢磨题解琢磨了好久，将问题转换，后面的$O(nlog^2n)$的DP还是很神的。。。

<!--more-->

### D Wide Flip

> 题意是给出一个01字符串$S(|S|\leq10^5)$，找到最大的$K$，使得每次翻转的区间都在至少为$K$的情况下，翻转为都是0的字符串。

二分过的，可以都变成0或者都变成1，每次从左到右check一边。

```C++
int n;
char val[maxn];
int num[maxn];
bool check(int x)
{
	int cnt = 0;
	memset(num, 0, sizeof(num));
	int end = -1;
	int st = -1;
	repp(i, 1, n)
	{
		if (val[i] == '1')
		{
			st = i;
			end = i + x - 1;
			break;
		}
	}
	if (end == -1)
	{
		return true;
	}
	repp(i, st, end)
	{
		int now = val[i] - '0';
		int op = now ^ (cnt & 1);
		if (op)
		{
			if (i + x - 1>n)
				return false;
			cnt++;
		}
	}
	return true;
}
 
bool check2(int x)
{
	int cnt = 0;
	memset(num, 0, sizeof(num));
	int end = -1;
	int st = -1;
	repp(i, 1, n)
	{
		if (val[i] == '0')
		{
			st = i;
			end = i + x - 1;
			break;
		}
	}
	if (end == -1)
	{
		return true;
	}
	repp(i, st, end)
	{
		int now = '1' - val[i];
		int op = now ^ (cnt & 1);
		if (op)
		{
			if (i + x - 1>n)
				return false;
			cnt++;
		}
	}
	return true;
}
void solve()
{
	scanf("%s", val + 1);
	n = strlen(val + 1);
	int le = 1;
	int ri = n;
	while (le<ri)
	{
		int mid = (le + ri + 1) / 2;
		if (check(mid) || check2(mid))
		{
			le = mid;
		}
		else
		{
			ri = mid - 1;
		}
	}
	cout << le;
}
```

### E Papple Sort

> 有都是小写字符的字符串$S(|S|\leq 2*10^5)$，每次交换相邻的字符，问最少交换多少次，该字符串变成回文串。

这种问题需要将问题化简，只考虑两对字符的相对位置与解决方法。

考虑两对字符的相对位置的话，有以下三种情况：

1)A…A……..B…B

2)A…B……..A…B

3)A…B……..B…A

这里会发现一个事情，三种情况无论哪一种，都是A可以在外围。即(l1,r1)和(l2,r2)当l1<l2时，(l1,r1)一定是外围字母。所以直接从左到右扫就好了，将每一个字母移动到最外围的位置。

```C++
char val[maxn];
ll num[maxn];
int lowbit(int x)
{
	return x&(-x);
}
ll getsum(ll x)
{
	ll ans = 0;
	while (x>0)
	{
		ans += num[x];
		x -= lowbit(x);
	}
	return ans;
}
ll add(int x, int v)
{
	int up = maxn - 1;
	while (x <= up)
	{
		num[x] += v;
		x += lowbit(x);
	}
	return x;
}
 
int vis[maxn];
int num_char[30];
vector<int>pos[30];
 
void solve()
{
	scanf("%s", val + 1);
	int len = strlen(val + 1);
 
	repp(i, 1, len)
	{
		num_char[val[i] - 'a']++;
		pos[val[i] - 'a'].push_back(i);
	}
	int cnt = 0;
	repp(i, 0, 26)
	{
		if (num_char[i] & 1)
		{
			cnt++;
		}
	}
	if (cnt >= 2)
	{
		puts("-1");
		return;
	}
	repp(i, 1, len)
	{
		add(i, 1);
		vis[i] = 1;
	}
	ll ans = 0;
	repp(i, 1, len)
	{
		if (vis[i] == 0)continue;
		int p = pos[val[i] - 'a'].back();
		pos[val[i] - 'a'].pop_back();
		if (p == i)
		{
			ans += ((getsum(len) - getsum(i)) >> 1);
		}
		else
		{
			ans += (getsum(len) - getsum(p));
		}
		vis[i] = 0;
		vis[p] = 0;
		add(p, -1);
		add(i, -1);
	}
	printf("%lld\n", ans);
}
 
```



### F Christmas Tree

> A个长度最多为B的链，通过图中的方式合并成一棵特定形状的树。要求(A,B)字典序最小。求A，B。

按照题解所说，可以看作是一棵树最多染成A种颜色的路径，每一条路径的长度最多是B。

可以发现，度为奇数的点才可以做成端点。所以，A最小就是奇数点S/2。在这种情况下，看每一条路径的最大程度可以是多少。二分B值。每一次check x，具体需要做树形DP，因为奇数点要做成端点，所以fa和x点之间的那一条边要放入到son中进行匹配。dp[x]表示该点向上的路径长度。如果son中有偶数的点，那么直接最左最右扫一次就好了，dp[x]=-1。奇数个son的话需要把一个点提出来，这时再做一次二分。

```C++
int n;
vector<int>edge[maxn];
int dp[maxn];
 
bool dfs(int x, int fa, int up)
{
	vector<int>son;
	if (edge[x].size() & 1)son.push_back(0);
	for (int i = 0; i<edge[x].size(); i++)
	{
		int to = edge[x][i];
		if (to == fa)continue;
		if (!dfs(to, x, up))return false;
		if(dp[to] != -1)son.push_back(dp[to] + 1);
	}
 
	sort(son.begin(), son.end());
 
	if (son.size() % 2 == 0)
	{
		int st = 0;
		int en = son.size() - 1;
		while (st < en)
		{
			if (son[st] + son[en] > up)
			{
				return false;
			}
			st++;
			en--;
		}
		dp[x] = -1;
	}
	else
	{
		int le = 0;
		int ri = son.size() - 1;
		int flag = 1;
		while (le < ri)
		{
			int mid = (le + ri) / 2;
 
			int i = 0;
			int j = son.size() - 1;
			flag = 1;
			while (i < j)
			{
				if (i == mid)i++;
				if (j == mid)j--;
				if (i<j)
				{
					if (son[i] + son[j] > up)
					{
						flag = 0;
						break;
					}
					else
					{
						i++;
						j--;
					}
				}
			}
			if (flag)
			{
				ri = mid;
			}
			else
			{
				le = mid + 1;
			}
		}
		if (son[le] > up)return false;
		int i = 0;
		int j = son.size() - 1;
		while (i < j)
		{
			if (i == le)i++;
			if (j == le)j--;
			if (i<j)
			{
				if (son[i] + son[j] > up)
				{
					return false;
				}
				else
				{
					i++;
					j--;
				}
			}
		}
		dp[x] = son[le];
	}
	return true;
}
 
void solve()
{
	sa(n);
	repp(i, 1, n - 1)
	{
		int x, y;
		sa(x), sa(y);
		edge[x].push_back(y);
		edge[y].push_back(x);
	}
	int cnt = 0;
	int p = 1;
	repp(i, 1, n)
	{
		if (edge[i].size() & 1)
		{
			cnt++;
		}
		if (edge[i].size() == 1)
		{
			p = i;
		}
	}
	int ans = cnt / 2;
	int le = 1;
	int ri = n;
	while (le < ri)
	{
		int mid = (le + ri) / 2;
		memset(dp, 0, sizeof(dp));
		if (dfs(p, -1, mid))
		{
			ri = mid;
		}
		else
		{
			le = mid + 1;
		}
	}
	cout << ans << " " << le << endl;
}
```

