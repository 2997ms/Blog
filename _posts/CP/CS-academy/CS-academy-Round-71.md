---
title: CS academy Round 71
date: 2018-03-03 11:57:35
tags: [CS academy]
categories: [题记]
---

CS academy Round 71

<!--more-->

[题目链接](https://csacademy.com/contest/round-71/tasks/)

### D Russian Dolls

> N个球每一个球有对应的大小，小的球可以嵌套在大的球里面。一个球只可以嵌套一个小球，但是可以递归嵌套。现在可以使$1$个大小为$X$的球变成$2$个大小为$X-1$的球，要求最终没有办法嵌套的时候，每一个球的大小是相同的。问最少操作多少次。

最终达到的情况就是最大的球数量最多。

从当前最大的球的数量开始枚举，看需要多少次操作。之后把最大的球变成两个次大的球，再枚举当前的数量需要多少次操作。

复杂度$O(log^2N)$。

```C++
ll n;
map<ll, ll>hax;

int split(map<ll, ll>&h, int mx)
{
	int value, num;
	tie(value, num) = *h.rbegin();

	if (num < mx || value == 1)
	{
		return 0;
	}
	h[value] = mx;
	h[value - 1] += (num - mx) * 2;
	return num - mx;
}

int cal(map<ll, ll>h, int up)
{
	int ans = 0;
	while (h.size())
	{
		int cur = split(h, up);
		if (cur >= up)return 1e9;
		ans += cur;

		int value, num;
		tie(value, num) = *h.rbegin();
		if (value == 1)
		{
			if (num>up)
			{
				return 1e9;
			}
			break;
		}
		h.erase(value);
	}
	return ans;
}

void solve()
{
	slla(n);
	int maxx = 0;
	repp(i, 1, n)
	{
		int x;
		sa(x);
		hax[x]++;
	}
	int ans = 1e9;
	int cnt = 0;
	for (int i = 0; i<30 && hax.size(); i++)
	{
		int value, num;
		tie(value, num) = *hax.rbegin();
		int tmp = cnt + cal(hax, num);
		ans = min(ans, tmp);

		cnt += split(hax, 0);
		hax.erase(hax.rbegin()->first);

		if (hax.size() && hax.rbegin()->second >= n)
		{
			ans = min(ans, cnt);
			break;
		}

	}
	cout << ans << endl;
}
```

### E Losing Nim

> 输出将和为$N$的数分成$K$堆，异或为$0$的方案数。每一堆至少为$1$，$K$从$1$到$N$。$1<N<500$

上来写了一个$O(N^4)$的dp。。。然后看着里面做的都是重复的操作，但是就不知道怎么优化到$O(N^3)$。

ans[i\][j\][k\]表示到第i堆，和为j，异或为k的方案数，然后枚举最后一个堆的情况。

TLE。

```C++
ll n,p;
ll ans[505][505][520];

void solve()
{
    slla(n),slla(p);
    repp(i,1,n)
    {
        ans[1][i][i]=1;
    }
    cout<<0<<endl;
    repp(i,2,n)
    {
        repp(last,1,n)
        {
            repp(presum,i-1,n)
            {
                if(last+presum>n)
                {
                    break;
                }
                repp(prexor, 0, 512)
                {
                    ans[i][last + presum][last ^ prexor] += ans[i - 1][presum][prexor];
                    ans[i][last + presum][last ^ prexor] %= p;
                }
            }
        }
        printf("%lld\n",ans[i][n][0]);
    }
}
```

后来问别人，发现可以dp[i\][k\]表示前i位和为k，异或为0的方案数。里面通过组合数转移。

```C++
ll n,p;
ll C[505][505];
ll dp[505][505];

void solve()
{
    slla(n),slla(p);
    C[0][0] = 1;
    repp(i,1,n)
    {
        C[i][0]=1;
        repp(j,1,i)
        {
            C[i][j] = C[i-1][j-1] + C[i-1][j];
            C[i][j] %= p;
            //DE(C[i][j])
        }
    }
    repp(i,1,n)
    {
        dp[i][0] = 1;
    }
    ll w = 1;
    while(w <= n)
    {
        repp(i,2,n)
        {
            for(int j=n;j>=0;j--)
            {
               for(int k=1;k<=n;k++)
               {
                   if(!dp[i][j])continue;
                   if(j + 2*k*w <= n && 2*k <= i) {
                       dp[i][j + 2 * k * w] += dp[i][j] * C[i][2 * k] % p;
                       dp[i][j + 2 * k * w] %= p;
                   }
                   else
                   {
                       break;
                   }
               }
            }
        }
        w *= 2;
    }
    repp(i,1,n)
    {
        rep(j,1,i)
        {
            dp[i][n] -= dp[j][n]*C[i][j] % p;
            dp[i][n] = (dp[i][n] % p + p) % p;
        }
        cout<<dp[i][n]<<endl;
    }
}
```

