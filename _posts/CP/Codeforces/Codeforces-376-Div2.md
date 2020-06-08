---
title: Codeforces 376 Div2
date: 2016-10-24 19:22:01
tags: [Codeforces, 博弈, DP]
categories: [题记]
---

[题目链接](http://codeforces.com/contest/731)与[代码链接](https://github.com/2997ms/My_Algorithm/tree/master/Codeforces/376_Div2)

晚上VP了这一场，并查集写脑残了，TLE47。F题关于数据应该注意数据范围，然后根据每一个数字来进行枚举。



### A Night at the Museum

> $26$个字母放在一个圆盘上，然后可以顺时针逆时针方向找字母，问总和旋转次数最少是多少。

每次贪心取最小值。res+=min(abs(k-cur),26-abs(k-cur))



### B Coupons and Discounts

> 有$n$天，对应每一天要购买相应数量的pizza，有两种购买方式，第一种一天买两个，第二种连续两天买一个。问该安排在这两种购买方式下是否可行。

从第一天开始贪心，偶数不用管，如果是奇数，那么想要可行下一天必须减1。判断每一天的数量大于等于0，还有最后一天要是偶数。



### C Socks

> 有n个袜子，k种颜色，m个天。每一天有对应安排，要穿哪两只袜子。问想要满足每一天袜子都一致的情况下，最小更改袜子的颜色数量。

并查集，把要穿的袜子放在一个集合里面。然后再在每一个集合里面选取出现颜色最多的那种，其他的袜子就要去改成这个颜色。。。



### D 80-th Level Archeology

> 题意是给出了$n$个序列，然后可以对所有序列向后移一位，最后一个$C$的话就变为$1$。问最少移多少位可以满足序列是呈现字典序递增的顺序。

对两个序列进行比较会发现，是一个合法区间。将所有合法区间进行合并，看有没有满足所有区间的数字。



### E Funny Game

> 题意是有$n$个数，Petya和Gena对这$n$个数按照如下规则去取：每次至少选择数列中最左面前$2$个数，其和计入分数，然后把这个和再次放入该数列的最左面，直到该数列只剩下一个数为止。每个人都希望自己的分数-对手的分数最大，Petya先取，问最优解。

后来才知道这种博弈是有套路的。。。

$DP[i\]$表示对当前玩家来说，取前$[1..i\]$数其最优值，那么这个最优值$DP[i\]$从哪里转移过来，从左往右取的话，这个最优值是取决于对手取的最优值，即是从右面转移过来的。根据这个可以写一个$O(n^2)$的记忆化$DP$。

```c++
int n;
int val[maxn],sum[maxn],dp[maxn];
int cal(int x)
{
	if(dp[x]!=-1)
	{
		return dp[x];
	}
	int res = -INF;
	for(int i=x+1;i<=n;i++)
	{
		res=max(res,cal(i));
	}
	if(res == -INF)
		res = 0;
	dp[x]=sum[x]-res;
	return dp[x];
}
void solve()
{
	memset(dp,-1,sizeof(dp));
	scanf("%d",&n);
	for(int i=1;i<=n;i++)
	{
		scanf("%d",&val[i]);
		sum[i]=sum[i-1]+val[i];
	}

	int res = -INF;
	for(int i=2;i<=n;i++)
	{
		res = max(res,cal(i));
	}
	printf("%d\n",res);
}
```

观察转移，从后转移的话，可以从后往前走，维护这个最大值，得到结果。

```C++
ll n;
ll val[maxn], sum[maxn];

void solve()
{
	scanf("%lld", &n);
	repp(i, 1, n)
	{
		scanf("%lld", &val[i]);
		sum[i] = sum[i - 1] + val[i];
	}
	ll res = 0;
	for (int i = n; i >= 2; i--)
	{
		if (i == n)res = sum[i];
		else if (res < sum[i] - res)
		{
			res = sum[i] - res;
		}
	}
	printf("%lld\n", res);
}

```

### F Video Cards

> 有$n$个数，可以以任意一个数作为开头，其余的数必须减小到是这个数的整数倍，记这个数列的和。问所有情况下的和的最大值。

注意数的范围都是小于200000的，那么就可以求前缀和，枚举每一个数作为基底，其2倍,3倍…的数。复杂度$O(nlogn)$