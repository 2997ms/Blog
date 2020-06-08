---
title: AtCoder Regular Contest 075
date: 2017-06-09 14:02:04
tags: [AtCoder]
categories: [题记]
---

这套题目整体来说算是比较简单的，前三个题目300、400、600分。主要是想记录一下D题。

[题目链接](http://arc075.contest.atcoder.jp/assignments)

<!--more-->

### C - Bugged

> 给出一组数($N<100$)，选择它们中的一部分或者全部，问其和$sum\%10$不为$0$的时候最大值是多少。

先算总和，看是否满足条件，满足条件直接输出。不满足条件枚举去掉的数字。

```C++
int n;
int val[maxn];
void solve()
{
	sa(n);
	int ans=0;
	repp(i,1,n)
	{
		sa(val[i]);
		ans+=val[i];
	}
	int maxx=0;
	if(ans%10)
	{
		printf("%d\n", ans);
		return;
	}
	repp(i,1,n)
	{
		int sum=ans-val[i];
		if(sum%10)maxx=max(maxx,sum);
	}
	printf("%d\n", maxx);
}
```



### D - Widespread

> 有一批怪物($N<10^5$)，每个怪物有自己的血量$h\_i$。你现在有一种攻击方式：选择其中一个怪物，造成$A$点伤害，其余的怪物造成$B$点伤害($B<A$)。问最少要攻击多少次让这些怪物的血量都变成$0$或者负值。

二分攻击次数。因为$A>B$，所以每次先对每个怪物减去$B\*x$的血量。剩下的相当于每次攻击造成$A-B$的伤害，看能否在$x$次攻击完。

```C++
ll n,a,b;
ll h[maxn];
ll val[maxn];
vector<int>v;
 
bool check(ll x)
{
	v.clear();
	repp(i,1,n)
	{
		val[i]=h[i]-x*b;
		if(val[i]>0)
			v.push_back(val[i]);
	}
	if(v.size()==0)return true;
	//sort(v.begin(),v.end());
 
	ll cnt=0;
	for(int i=0;i<v.size();i++)
	{
		cnt+= (v[i]+a-b-1)/(a-b);
	}
	return cnt<=x;
}
 
void solve()
{
	scanf("%lld%lld%lld",&n,&a,&b);
	repp(i,1,n)
	{
		scanf("%lld",&h[i]);
	}
	ll le=0,ri=1e9+5;
	while(le<ri)
	{
		ll mid=(le+ri)/2;
		if(check(mid))
		{
			ri=mid;
		}
		else
		{
			le=mid+1;
		}
	}
	printf("%lld\n",le);
}
```



### E - Meaningful Mean

> 给出一组数($N<2\*10^5$)，一共有$\frac{N\*(N+1)}{2}$个连续的子序列，问在这些子序列中有多少个序列的平均值大于给定的$K$。

对数组中每一个元素都减去$K$，会发现剩下的就是经典的逆序对问题了。

```C++
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
 
ll n, k;
ll val[maxn], sum[maxn];
ll stmp[maxn], s[maxn];
 
void solve()
{
	scanf("%lld%lld", &n, &k);
	repp(i, 1, n)
	{
		scanf("%lld", &val[i]);
		val[i] -= k;
		sum[i] = sum[i - 1] + val[i];
		stmp[i] = sum[i];
	}
	sort(stmp + 1, stmp + n + 1);
	repp(i, 1, n)
	{
		s[i] = lower_bound(stmp + 1, stmp + n + 1, sum[i]) - stmp;
	}
	ll ans = 0;
	repp(i, 1, n)
	{
		if (sum[i] >= 0)ans++;
		ans +=  (getsum(s[i]));
		add(s[i], 1);
	}
	printf("%lld\n", ans);
}
```



### F - Mirrored

> 题意是$N$，和它的逆序$rev(N)$，然后给出的是$D$，问有多少个$N$满足$rev(N)=N+D$。

和萤火虫讨论了很久。。。按位去考虑每一个值，具体得似乎很难表达出来。感觉这个就是很纯的想法题啊。。。

```C++
ll num[22];
 
ll dfs(int le, int ri, ll d)
{
	if (le >= ri)
	{
		if (d == 0)
		{
			if (le == ri)
			{
				return 10;
			}
			else
			{
				return 1;
			}
		}
		else
		{
			return 0;
		}
	}
	int t = (10 - d % 10) % 10;
	ll sur = d - t*num[ri - le] + t;
	return ((le ? 10 : 9) - t)*(dfs(le + 1, ri - 1, llabs(sur) / 10));
}
 
int main()
{
	num[0] = 1;
	for (int i = 1; i <= 20; i++)
	{
		num[i] = num[i - 1] * 10;
	}
	ll d;
	scanf("%lld", &d);
	ll ans = 0;
	for (int i = 0; i < 19; i++)
	{
		ans += dfs(0, i, d);
	}
	printf("%lld", ans);
	//system("pause");
	return 0;
}
```