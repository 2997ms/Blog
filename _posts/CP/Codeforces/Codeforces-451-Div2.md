---
title: Codeforces 451 Div2
date: 2017-12-26 15:15:31
tags: [Codeforces, Hash]
categories: [题记]
---

其实451和452两场就真的挺简单的了，但就是脑残了。

写一下补的题目吧。其余的题目都很水了。

<!--more-->

D题贪心，从左到右扫，不满足的时候就把最右边的删掉就好了。我居然写了个树状数组。。。(脑残。。。然后判断的时候一种情况没考虑到就WA掉了。。。

E题就直接分情况讨论就好了，把多于n/2的那部分贪心移过去。

F题可以说一下哈。给定一个字符串，要写成X+Y=Z的形式。字符串长度$10^6$级别。

首先肯定是枚举第一个数的分隔的位置。。。然后，我就不知道怎么做了。。。

**X+Y=Z这种，Z的长度要么和X相同，要么比X多1，要么和Y相同，要么比Y多1。就这四种情况啊。这样就完了啊。**

**然后怎么判断加法呢，前缀和哈希一下**，这里我还是trick，运气好了过去了。比赛里面还是要写两个hash check的。。(当初惨痛的教训。。。

```C++
int len;
char val[maxn];
ll inv[maxn], pre[maxn], f[maxn];

bool check(int st, int en)
{

	int len1 = st;
	int len2 = en - st;
	int len3 = len - en;
	if (len1 > len3 || len2 > len3)return false;
	if (len1 <= 0 || len2 <= 0 || len3 <= 0)return false;

	if (len1>1 && val[1] == '0')return false;
	if (len2>1 && val[st + 1] == '0')return false;
	if (len3>1 && val[en + 1] == '0')return false;

	ll sum1 = pre[st];
	ll sum2 = (pre[en] - pre[st] * f[en - st] % mod) % mod;
	sum2 = (sum2%mod + mod) % mod;
	ll sum3 = (pre[len] - pre[en] * f[len - en] % mod) % mod;
	sum3 = (sum3%mod + mod) % mod;
	if ((sum1 + sum2)%mod != sum3)return false;
	for (int i = 1; i <= len; i++)
	{
		printf("%c", val[i]);
		if (i == st)printf("+");
		if (i == en)printf("=");
	}
	return true;
}

void solve()
{
	scanf("%s", val + 1);
	len = strlen(val + 1);
	inv[0] = 1;
	f[0] = 1;
	int up = 1e6;
	repp(i, 1, up)
	{
		f[i] = f[i-1] * 10 % mod;
	}
	repp(i, 1, len)
	{
		pre[i] = pre[i - 1] * 10 + val[i] - '0';
		pre[i] %= mod;
	}

	repp(i, 1, len)
	{
		if (check(i, len - i))
		{
			return;
		}
		if (check(i, len - i - 1))
		{
			return;
		}
		if (check(len - i - i, len - i))
		{
			return;
		}
		if (check(len - i - i - 1, len - i - 1))
		{
			return;
		}
	}
}

```

