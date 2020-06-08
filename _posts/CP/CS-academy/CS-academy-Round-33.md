---
title: CS academy Round 33
date: 2017-06-15 21:03:33
tags: [CS academy,DP,贪心]
categories: [题记]
---

[题目链接](https://csacademy.com/contest/round-33/tasks/)

还是需要沉浸下去，C、D、E都不错的题目，E题DP应该是要想出来的啊，怎么回事啊。

<!--more-->

### A Bounding Box

> $N$个点，坐标$x\_i,y\_i$，要用最小的矩形(边要平行坐标轴)包住这些点，问最小矩形面积。

求最大最小值。

```C++
int n;
int a[maxn],b[maxn];
void solve()
{
	sa(n);
	repp(i,1,n)
	{
		sa(a[i]),sa(b[i]);
	}
	int amin=a[1],amax=a[1];
	int bmin=b[1],bmax=b[1];
	repp(i,1,n)
	{
		amin=min(amin,a[i]);
		amax=max(amax,a[i]);
		bmin=min(bmin,b[i]);
		bmax=max(bmax,b[i]);
	}
	cout<<(amax-amin)*(bmax-bmin)<<endl;
}
```



### B Numbers Tournament

> 按照题意模拟一下就好饿。。。

```C++
int n;
int cnt[maxn][10005];
int val[maxn][maxn];
int ans[maxn];
vector< pair<int, int> >v;
int res[maxn];

void solve()
{
	sa(n);
	repp(i, 1, n)
	{
		repp(j, 1, n)
		{
			sa(val[i][j]);
			cnt[i][val[i][j]]++;
		}
		sort(val[i] + 1, val[i] + n + 1);
	}
	repp(i, 1, n)
	{
		repp(j, 1, n)
		{
			if (i == j)continue;
			int low = -1, high = -1;
			repp(k, 1, n)
			{
				if (cnt[j][val[i][k]])
				{
					if (low == -1)low = val[i][k];
					else low = min(low, val[i][k]);

					if (high == -1)high = val[i][k];
					else high = max(high, val[i][k]);
				}
			}
			if (low == high)
			{
				ans[i]++;
				continue;
			}
			int c1 = 0, c2 = 0;

			repp(k, 1, n)
			{
				if (val[i][k]<low || val[i][k]>high)c1++;
				if (val[j][k]<low || val[j][k]>high)c2++;
			}
			if (c1>c2)ans[i] += 2;
			else if (c1 == c2)ans[i]++;
		}
	}
	repp(i, 1, n)
	{
		//DE(ans[i])
		v.push_back(mp(ans[i], -i));
	}
	sort(v.begin(), v.end());
	reverse(v.begin(), v.end());
	rep(i, 0, v.size())
	{
		printf("%d\n", -v[i].second);
	}

}
```



### C Div 3

> 题意给出$X(X<10^9)$，可以对$X$加$1$，减$1$，除以$3$(如果可以整除$3$)，问最少多少次操作变成$1$。

分析下来就是个贪心啊，能除则除，不能则凑啊。。。

```C++
int dfs(int x)
{
	if(x==2)return 1;
	if(x==1)return 0;
	if(x%3==0)return 1+dfs(x/3);
	if(x%3==1)return 1+dfs(x-1);
	if(x%3==2)return 1+dfs(x+1);
}
void solve()
{
	int x;
	sa(x);
	printf("%d\n", dfs(x));
}
```



### D Subinterval Division

> 题意是在$X$坐标轴上有一条线段$(0,X)$，现在有$N$个点$(x\_i,y\_i)(x\_i>0,y\_i>0)$，要求把这些线段分成若干份，使得每一份线段上的点距离这N个点的距离最短的那个点是同一个点，问每个点控制的区间线段长度。

题意有些绕。。。我看了好久。。。

如果是两个点，那么做这两个点的垂直平分线交与$X$轴，左边就是距离前面那个点比较小的线段区间，右边就是后面那个点的区间。

$N$个点，按照$X$轴排序。同理维护一个栈。栈里每一个元素都是当前可以控制的最远距离，遇到之前的位置了，把栈顶弹出去。

```C++
int n;
double x;
int nx[maxn], ny[maxn];
map< pair<int, int>, int>hax;
map<int, int>px;
vector< pair<int, int> >sta;
double ans[maxn];

double Insec(pair<int, int> a, pair<int, int>b)
{
	double k = 1.0*(a.second - b.second) / (a.first - b.first);
	double x = (a.first + b.first) / 2.0;
	double y = (a.second + b.second) / 2.0;

	return x + y*k;
}

void solve()
{
	sa(n);
	scanf("%lf", &x);
	repp(i, 1, n)
	{
		int a, b;
		sa(a), sa(b);
		if (px.find(a) == px.end() || px[a]>b)
		{
			px[a] = b;
		}
		hax[mp(a, b)] = i;
	}
	for (auto it : px)
	{
		while (1)
		{
			if (sta.size()<2)
			{
				break;
			}
			auto a = *(sta.end() - 1);
			auto b = *(sta.end() - 2);
			double A = Insec(b, a);
			double B = Insec(a, it);
			if (A>B)
			{
				sta.pop_back();
			}
			else
			{
				break;
			}
		}
		sta.push_back(it);
	}
	double left = 0;
	for (int i = 0; i + 1<sta.size(); i++)
	{
		int id = hax[sta[i]];
		double now = Insec(sta[i], sta[i + 1]);
		now = min(now, x);
		if (now > left)
		{
			ans[id] = now - left;
		}
		left = max(left, now);
	}
	int id = hax[sta[sta.size() - 1]];
	double now = Insec(sta[sta.size() - 2], sta[sta.size() - 1]);
	if (x > now)
	{
		ans[id] = x - now;
	}


	for (int i = 1; i <= n; i++)
	{
		printf("%.7lf\n", ans[i]);
	}
}
```



### E Free Palindromes

> 题意：长度为$N$的回文串，要求前面$N-1$个前缀都不是回文串，这样的回文串是符合要求的。现在每一个位置有$K$个字符可以选，问符合要求的字符串有多少种。

对于长度为X的回文串，有$K^{(\frac{x+1}{2})}$种，减去不符合的。即前面有是回文串的情况。

现在有结论，如果前缀有回文串，那么最短的长度一定小于等于$N/2$。假设最短的长度$H$大于$N/2$，那么设$L=H-N/2$。这样因为整个是个字符串，那么中间的$2\*L$也是字符串，对称相等。同理前面的那个前缀的$2\*L$也是对称相等，最短的长度会发现就是$2\*L$了。

这时考虑dp。dp[x]表示长度为x，符合要求的字符串。此时有：
$$
dp[x\]=k^{\frac{x+1}{2}}-\sum\_{j=2}^{\frac{i}{2}}dp[j\]*k^{\frac{i+1}{2}-j}
$$
后面部分可以用前缀和记录。

```C++
ll n,k;
ll dp[maxn];

void solve()
{
	scanf("%lld%lld",&n,&k);

	ll sum=0;
	repp(i,1,n)
	{
		dp[i] = po(k,(i+1)/2,mod);
		if(i%2==1&&i>2)
		{
			sum = sum*k%mod+dp[(i+1)/2]%mod;
			sum %= mod;
		}
		//DE(dp[i])
		//DE(sum)
		dp[i] -= sum;
		dp[i] = (dp[i]%mod+mod)%mod;
	}

	printf("%lld",dp[n]);
}

```



