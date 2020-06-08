---
title: AtCoder Regular Contest 077
date: 2017-07-30 14:56:21
tags: [AtCoder, KMP]
categories: [题记]
---

也许是因为最近在实习的缘故，也许不是(那你说它干嘛).......可能就是因为懒......最近做题特别少，但是感觉.......好像又到了一些关键时期(…….)，所以还是需要做一做的，然后发现本就没有多少的IQ似乎最近清零了一样.......

这次的E题和F题都想了好久好久好久，AtCoder这种乱搞题我特别喜欢，因为.......常常做不出来.....(摊手苦笑)

<!--more-->

[题目链接](http://arc077.contest.atcoder.jp/)

### C - pushpush

> 给出了一个序列$a\_i$，$n$次操作之后得到序列$b$。
>
> 1.每次操作内容为把$a\_i$放到当前序列$b$的后面。
>
> 2.reverse当前b序列。
>
> 求经过$n$次操作之后的$b$序列。

找规律，一左一右。

```C++
int n;
int val[maxn];
int ans[maxn];
 
void solve()
{
	sa(n);
	repp(i,1,n)
	{
		sa(val[i]);
	}
	int left=1;
	int right=n;
	int cnt=n;
	repp(i,1,n)
	{
		if(i&1)
		{
			ans[left]=val[cnt];
			left++;
		}
		else
		{
			ans[right]=val[cnt];
			right--;
		}
		cnt--;
	}
	repp(i,1,n)
	{
		if(i>1)printf(" ");
		printf("%d", ans[i]);
	}
	puts("");
}
```



### D - 11

> 给出$n+1$个数，其中$1$到$n$至少会出现$1$次，有$1$个数会出现$2$次。问从这个序列中取出的$1$到$n$子序列分别有多少种。

组合数，减掉重复的部分。其中重复的部分，就是重复出现的一前一后取$i-1$个，这部分算重复了。

```C++
int n;
int val[maxn];
ll fac[maxn],inv[maxn];
ll ans[maxn];
 
ll C(ll x,ll y)
{
	return fac[x]*inv[y]%mod*inv[x-y]%mod;
}
 
void init()
{
	fac[0]=1;
	inv[0]=1;
	repp(i,1,200005)
	{
		fac[i]=fac[i-1]*i%mod;
		inv[i]=po(fac[i],mod-2,mod);
	}
}
 
map<int,int>hax;
void solve()
{
	sa(n);
	n++;
	int st=0,en=n;
	repp(i,1,n)
	{
		sa(val[i]);
		if(hax.count(val[i]))
		{
			st=hax[val[i]];
			en=i;
		}
		hax[val[i]]=i;
	}
	//DE(st)
	//DE(en)
	repp(i,1,n)
	{
		ans[i]=C(n,i);
	}
	ll left = st-1;
	ll right = n-en;
	//DE(left)
	//DE(right)
	repp(i,1,n)
	{
		if(i-1>right+left)
		{
			printf("%lld\n", ans[i]);
			continue;
		}
		ans[i]=ans[i]-C(left+right,i-1);
		ans[i]=(ans[i]%mod+mod)%mod;
		printf("%lld\n",ans[i]);
	}
}
```



### E - guruguru

>有一盏有各个等级亮度的灯，正常情况下你可以按forward按钮，亮度就不停的+1，到达n之后，亮度就变成1。现在你可以设置一个favourite按钮，按f可以直接跳转到你设置的亮度。
>
>现在有一个亮度的序列，问在你设置favourite按钮有n种选择的情况下，最少需要按多少次。

这题好玩啊。。。

如果$val[i\]>val[i-1\]$，那么假设你$f$值在线段$val[i-1\]$到$val[i\]$上，那么会减少$f-val[i\]-1$次。用两个数组维护这个值，一个维护$-val[i\]-1$，一个维护$+f$(只需$+1$，最后$\*i$就好了)。如果$val[i\]<val[i-1\]$，同理。

其实用线段树好像更好想，但是不如这个好写。。。

```C++
int n,m;
ll val[maxn],b[maxn],c[maxn];
ll ans;
 
void solve()
{
	ans=0;
	scanf("%d%d",&n,&m);
	repp(i,1,n)
	{
		scanf("%lld",&val[i]);
	}
	repp(i,2,n)
	{
		if(val[i-1]<val[i])
		{
			ans+=val[i]-val[i-1];
			
			b[val[i-1]+1]+=val[i-1]+1;
			b[val[i]+1]-=val[i-1]+1;
			++c[val[i-1]+1];
			--c[val[i]+1];
		}
		else
		{
			ans += m+val[i]-val[i-1];
 
			b[1]-=m-val[i-1]-1;
			b[val[i]+1]+=m-val[i-1]-1;
			b[val[i-1]+1]+=val[i-1]+1;
 
			c[1]++;
			c[val[i]+1]--;
			c[val[i-1]+1]++;
		}
	}
	for(int i=1;i<=m;i++)
	{
		b[i]+=b[i-1];
		c[i]+=c[i-1];
	}
	ll cnt=0;
	for(int i=1;i<=m;i++)
	{
		cnt=max(cnt,1LL*i*c[i]-b[i]);
 
	}
	//DE(cnt)
	printf("%lld\n", ans-cnt);
}

```



### F - SS

> 有一个偶数序列SS，现在在后面添加至少一个字符，还是要SS形式，添加最少数量的字符。这样一直一直添加下去，会形成一个无穷长度的序列，给出left，right，问这期间各个字母的数量。

假设这个S是XTX形式的，既前k个字符与后k个字符相等，即是这个X，其余部分是T。

那么最少添加多少个呢，会发现是这样的，XTXXTX|TXXT，即TXXT就是要添加的字符数量，有多少个呢，算一下是2*(n-k)个。这样根据题意我们只关注前半部分，g(S)=SG，其中S=XTX，G=XT。

然后现在你有XTXXTXTXXT了，下次操作还要添加多少个呢，XTXXTXTXXT|XXTXTX，现在的g(SG)=SGS。

这样递推下去发现是字符串的斐波那契。。。好神。。。

```C++
char val[maxn];
ll le, ri;
 
int len, prod;
ll pre[maxn][30];
int nxt[maxn];
 
ll fib(ll n, ll x)
{
	if (n <= len) {
		return pre[n][x];
	}
	if (n <= 2 * len) {
		return pre[len][x] + pre[n - len][x];
	}
	ll ans1 = pre[len][x] + pre[prod][x];
	ll ans2 = pre[len][x];
	ll a = len + prod;
	ll b = len;
 
	while (n > a + b)
	{
		ll tmp = ans1;
		ans1 += ans2;
		ans2 = tmp;
 
		tmp = a;
		a += b;
		b = tmp;
	}
	return ans1 + fib(n - a, x);
}
 
ll cal(ll n, ll x)
{
	if (n <= len)return pre[n][x];
	if (len%prod == 0)
	{
		ll res = 1LL * (n - len) / prod*pre[prod][x] + pre[len][x];
		n -= len;
		n %= prod;
		return res + pre[n][x];
	}
	else
	{
		return fib(n, x);
	}
}
 
void solve()
{
	memset(pre, 0, sizeof(pre));
	scanf("%s", val + 1);
 
	len = strlen(val + 1);
	len /= 2;
 
	nxt[0] = -1;
	int k = -1;
	for (int i = 0; i < len;)
	{
		if (k == -1 || val[k + 1] == val[i + 1])
		{
			k++;
			i++;
			nxt[i] = k;
		}
		else
		{
			k = nxt[k];
		}
	}
	prod = len - nxt[len];
 
	repp(i, 1, len)
	{
		for (int k = 0; k < 26; k++)
		{
			pre[i][k] = pre[i - 1][k];
		}
		pre[i][val[i] - 'a']++;
	}
	scanf("%lld%lld", &le, &ri);
 
	rep(k, 0, 26)
	{
		printf("%lld ", cal(ri, k) - cal(le - 1, k));
	}
}
```

