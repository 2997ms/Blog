---
title: 51nod填坑记(一)(1742,1810,1753,1291,1296)
date: 2017-10-02 21:28:32
tags: [51nod]
categories: 题记
---

做51nod也挺久的了，因为早期总是喜欢看题解错过了不少好题，而今想来想给自己几个耳光。重要的永远不是这个题目做不做得出来，而是如何去思考。这里很多题目都不错，想在这里记录一下。

<!--more-->

### [51nod 1742 开心的小Q](http://www.51nod.com/onlineJudge/problemSolution.html#!problemId=1742)

这个题目出得很赞啊。。。不会啊。。。或许是这类题目做得实在太少的缘故，式子的推导+容斥，不会啊。。。

趁着我还没有失忆。。总结一下。

题目中要求的是有趣约数的总数，有趣的数定义是大于1的完全平方数。

这个时候考虑莫比乌斯函数，这样题目要求的即为：
$$
S(n) = \sum\_{i=1}^{n}\sum\_{d|i}(1-|u(d)|)
$$
所求的是
$$
S(b)-S(a-1)
$$
然后有
$$
S(n) = \sum\_{i=1}^{n}\sum\_{d|i}(1-|u(d)|) = \sum\_{x=1}^{n}\sum\_{d=1}^{\frac{x}{d}}(1-|u(d)|) = \sum\_{x=1}^{n}F(\frac{n}{x})
$$

$$
F(n)=\sum\_{i=1}^{n}(1-|u(i)|)
$$

这个时候可以发现$F(x)$代表的意义是不超过$n$的有平方因子的个数。这个时候发现是可以容斥来解决的。。。
$$
F(n)=n-\sum\_{i=1}^{\sqrt{n}}u(i)*(\frac{n}{i^2})
$$
这个式子的意义就是一共是n个，减去所有无平方因子的个数。后面的容斥为什么就是想不到啊，好笨啊。。。

```C++
int vis[maxn];
int pri[maxn];
int u[maxn];
void init()
{
	int i, j, k;
	u[1] = 1;
	int cnt = 0;
	for (i = 2; i <= 1e5; i++)
	{
		if (!vis[i])
		{
			cnt++;
			pri[cnt] = i;
			u[i] = -1;
		}
		for (j = 1; j <= cnt && 1LL*pri[j] * i <= 1e5; j++)
		{
			vis[i*pri[j]] = 1;
			if (i%pri[j] == 0)
			{
				u[i*pri[j]] = 0;
				break;
			}
			else
			{
				u[i*pri[j]] = -u[i];
			}
		}
	}
}

ll F(ll n)
{
	if(n==0)return 0;
	ll ans = n;
	for(int i=1;i*i<=n;i++)
	{
		ans-=u[i]*(n/(i*i));
	}
	return ans;
}

ll S(ll n)
{
	ll ans=0;
	ll cnt=1;
	for(int i=1;i<=n&&cnt;i+=cnt)
	{
		int ri = n/i;
		cnt = n/ri - n/(ri+1);
		ans += cnt*F(ri);
	}
	return ans;
}

void solve()
{
	ll a,b;
	cin>>a>>b;
	cout<<S(b)-S(a-1)<<endl;
}
```

### [51nod 1810 连续区间](http://www.51nod.com/onlineJudge/questionCode.html#!problemId=1810)

按照题意“连续区间”就是这个区间的最大值与最小值之间的差值等于个数。

考虑分治。。。。。。。。。。

就考虑一个端点在中心的左面，一个端点在中心的右面，如果可以O(n)搞出来，就可以考虑分治。。。。

具体左端点是i，右端点是j，然后讨论Max、Min来源。



```C++
ll ans;
int n;
int val[maxn];
int cnt[maxn * 3];
int maxx[maxn], minn[maxn];

int read() {
    char ch;
    for(ch=getchar();ch<'0'||ch>'9';ch=getchar());
    int x=ch-'0';
    for(ch=getchar();ch>='0'&&ch<='9';ch=getchar()) x=x*10+ch-'0';
    return x;
}

void cal(int le, int ri)
{
	int mid = (le + ri) / 2;
	//le,mid
	maxx[mid] = minn[mid] = val[mid];
	for (int i = mid - 1; i >= le; i--)
	{
		maxx[i] = max(maxx[i + 1], val[i]);
		minn[i] = min(minn[i + 1], val[i]);
	}

	maxx[mid + 1] = minn[mid + 1] = val[mid + 1];
	for (int i = mid + 2; i <= ri; i++)
	{
		maxx[i] = max(maxx[i - 1], val[i]);
		minn[i] = min(minn[i - 1], val[i]);
	}

	//for (int i = le; i <= mid; i++)
	for (int i = mid; i >= le; i--)
	{
		int j = maxx[i] - minn[i] + i;
		if (j > mid&&j <= ri&&maxx[i] > maxx[j] && minn[i] < minn[j])
		{
			ans++;
		}
	}
	for (int j = mid + 1; j <= ri; j++)
	{
		int i = -(maxx[j] - minn[j] - j);
		if (i >= le&&i <= mid&&maxx[j]>maxx[i] && minn[j]<minn[i])ans++;
	}

	int t_le = mid + 1, t_ri = mid;
	for (int i = mid; i >= le; i--)
	{
		while (t_ri<ri&&maxx[t_ri + 1]<maxx[i])
		{
			t_ri++;
			cnt[minn[t_ri] + t_ri + maxn]++;
		}
		while (t_le <= t_ri&&minn[t_le]>minn[i])
		{
			cnt[minn[t_le] + t_le + maxn]--;
			t_le++;
		}
		ans += cnt[maxx[i] + i + maxn];
	}
	while (t_le <= t_ri)
	{
		cnt[minn[t_le] + t_le + maxn]--;
		t_le++;
	}


	t_le = mid + 1, t_ri = mid;

	for (int j = mid + 1; j <= ri; j++)
	{
		while (t_le>le&&maxx[t_le - 1]<maxx[j])
		{
			t_le--;
			cnt[minn[t_le] - t_le + maxn]++;
		}
		while (t_le <= t_ri&&minn[t_ri]>minn[j])
		{
			cnt[minn[t_ri] - t_ri + maxn]--;
			t_ri--;
		}
		ans += cnt[maxx[j] - j + maxn];
	}
	while (t_le <= t_ri)
	{
		cnt[minn[t_ri] - t_ri + maxn]--;
		t_ri--;
	}

	if (le == ri)
	{
		return;
	}
	cal(le, mid);
	cal(mid + 1, ri);
}

void solve()
{
	sa(n);
	repp(i, 1, n)val[i]=read();

	ans = 0;
	cal(1, n);
	printf("%lld\n", ans + n);
}
```

### [51nod 1753 相似子串](http://www.51nod.com/onlineJudge/questionCode.html#!problemId=1753)

构造26个01字符串，然后每一次并查集合并，字符串哈希，相差一个的话，一定差的是$p\_i^k$，记录一下。

```C++
ll n;
char val[maxn];
ll fac[maxn][2];
bool Hash1[mod+2];
bool Hash2[mod+2];
int sum[maxn][27][2];
int fa[30];
ll v[27][3][2];

int getfa(int x)
{
	if(x==fa[x])return x;
	else 
	{
		return fa[x]=getfa(fa[x]);
	}
}

void uni(int x,int y)
{
	x=getfa(x);
	y=getfa(y);
	if(x==y)return;
	fa[x]=y;
	v[y][1][0]+=v[x][1][0];
	v[y][1][1]+=v[x][1][1];

	v[y][1][0]=(v[y][1][0]%mod+mod)%mod;
	v[y][1][1]=(v[y][1][1]%mod+mod)%mod;

	v[y][2][0]+=v[x][2][0];
	v[y][2][1]+=v[x][2][1];

	v[y][2][0]=(v[y][2][0]%mod+mod)%mod;
	v[y][2][1]=(v[y][2][1]%mod+mod)%mod;
}

void cal()
{
	int t,le1,le2,ri1,ri2;
	sa(t),sa(le1),sa(ri1),sa(le2),sa(ri2);
	if(ri1-le1!=ri2-le2)
	{
		puts("NO");
		return;
	}
	repp(k,'a'-'a','z'-'a')
	{
		fa[k]=k;
		v[k][1][0] = sum[ri1][k][0]-sum[le1-1][k][0];
		v[k][1][0] = (v[k][1][0]%mod+mod)%mod;

		v[k][1][1] = sum[ri2][k][0]-sum[le2-1][k][0];
		v[k][1][1] = (v[k][1][1]%mod+mod)%mod;

		if(le1<le2)
		{
			v[k][1][0] *= fac[le2-le1][0];
			v[k][1][0] = (v[k][1][0]%mod+mod)%mod;
		}
		else
		{
			v[k][1][1] *= fac[le1-le2][0];
			v[k][1][1] = (v[k][1][1]%mod+mod)%mod;
		}

		v[k][2][0] = sum[ri1][k][1]-sum[le1-1][k][1];
		v[k][2][0] = (v[k][2][0]%mod+mod)%mod;

		v[k][2][1] = sum[ri2][k][1]-sum[le2-1][k][1];
		v[k][2][1] = (v[k][2][1]%mod+mod)%mod;

		if(le1<le2)
		{
			v[k][2][0]*=fac[le2-le1][1];
			v[k][2][0] = (v[k][2][0]%mod+mod)%mod;
		}
		else
		{
			v[k][2][1]*=fac[le1-le2][1];
			v[k][2][1] = (v[k][2][1]%mod+mod)%mod;
		}
	}
	char h[5];
	repp(i,1,t)
	{
		scanf("%s",h+1);
		int k1 = h[1]-'a';
		int k2 = h[2]-'a';
		uni(k1,k2);
	}
	int cnt=0;
	repp(k,'a'-'a','z'-'a')
	{
		if(k!=fa[k])continue;
		if(v[k][1][0]!=v[k][1][1])
		{
			cnt++;
			if(cnt>2)
			{
				puts("NO");
				return;
			}
			ll h1 = v[k][1][0]-v[k][1][1];
			h1=(h1%mod+mod)%mod;
			ll h2 = v[k][2][0]-v[k][2][1];
			h2=(h2%mod+mod)%mod;
			if(!((Hash1[h1]&&Hash2[h2])||(Hash1[mod-h1]&&Hash2[mod-h2])))
			{
				puts("NO");
				return;
			}
		}
	}
	puts("YES");
}

void solve()
{
	scanf("%s",val+1);
	n = strlen(val+1);
	fac[0][0]=fac[0][1]=1;
	repp(i,1,n)
	{
		fac[i][0]=fac[i-1][0]*29%mod;Hash1[fac[i][0]]=1;
		fac[i][1]=fac[i-1][1]*13%mod;Hash2[fac[i][1]]=1;
	}
	repp(k,'a','z')
	{
		repp(i,1,n)
		{
			sum[i][k-'a'][0]=sum[i-1][k-'a'][0]+ (val[i]==k)*fac[i][0]%mod;
			sum[i][k-'a'][0]=(sum[i][k-'a'][0]%mod+mod)%mod;

			sum[i][k-'a'][1]=sum[i-1][k-'a'][1]+ (val[i]==k)*fac[i][1]%mod;
			sum[i][k-'a'][1]=(sum[i][k-'a'][1]%mod+mod)%mod;
		}
	}
	int t;
	sa(t);
	while(t--)
	{
		cal();
	}
}
```



### [51nod 1291 Farmer](http://www.51nod.com/onlineJudge/questionCode.html#!problemId=1291)

单调栈，一层一层添加，然后+1，-1。。。

```C++
int m,n;
int val[maxn][maxn];
char v[maxn];
int top,sta[maxn],hei[maxn];
int ans[maxn][maxn];

void solve()
{
	sa(m),sa(n);
	repp(i,1,n)
	{
		scanf("%s",v+1);
		repp(j,1,m)
		{
			hei[j] = (v[j]=='1'?hei[j]+1:0);
		}
		top=0;
		sta[++top] = 0;
		repp(k,1,m+1)
		{
			while(hei[k]<hei[sta[top]])
			{
				ans[max(hei[k],hei[sta[top-1]])+1][k-sta[top-1]-1]++;
				ans[hei[sta[top]]+1][k-sta[top-1]-1]--;
				top--;
			}
			while(top&&hei[sta[top]]==hei[k])top--;
			sta[++top]=k;
		}
	}
	repp(i,1,n)
	{
		repp(j,1,m)
		{
			ans[i][j]+=ans[i-1][j];
		}
	}
	repp(i,1,n)
	{
		for(int k=m-1;k>=1;k--)
		{
			ans[i][k]+=ans[i][k+1];
		}
		for(int k=m-1;k>=1;k--)
		{
			ans[i][k]+=ans[i][k+1];
		}
	}
	repp(i,1,n)
	{
		repp(k,1,m)
		{
			if(k>1)printf(" ");
			printf("%d", ans[i][k]);
		}
		printf("\n");
	}
}
```



### [51nod1296 有限制的排列](http://www.51nod.com/onlineJudge/questionCode.html#!problemId=1296)

考虑一个排列是怎么生成的。当在最后位置添加i时，之前比i大的数字自动+1。

dp[i\][j]表示i个数字，j作为最后一个数字的合法排列数量。转移的时候很明显是一个前缀和的转移。

```C++
int n,k1,k2;
int vis[5005];
int dp[5005][5005];
// 1 xiaoyu
int sum[5005];

void solve()
{
	sa(n),sa(k1),sa(k2);
	repp(i,1,k1)
	{
		int x;
		sa(x);
		vis[x+1]=1;
		vis[x+2]=2;
	}
	repp(i,1,k2)
	{
		int x;
		sa(x);
		if(vis[x+1]==1)
		{
		    puts("0");
		    return;
		}
		vis[x+1]=2;
		vis[x+2]=1;
	}
	dp[1][1] = sum[1] = 1;
	repp(i,2,n)
	{
		repp(j,1,i)
		{
			if(vis[i]==1)
			{
				dp[i][j] = sum[i-1]-sum[j-1];
				dp[i][j] = (dp[i][j]%mod+mod)%mod;
			}
			else if(vis[i]==2)
			{
				dp[i][j] = sum[j-1];
			}
			else
			{
				dp[i][j] = sum[i-1];
			}
		}
		memset(sum,0,sizeof(sum));
		repp(j,1,i)
		{
			sum[j]=sum[j-1]+dp[i][j];
			sum[j]=(sum[j]%mod+mod)%mod;
		}
	}
	int ans=0;
	repp(i,1,n)
	{
		ans = ans + dp[n][i];
		while(ans>=mod)ans-=mod;
	}
	printf("%d\n", ans);
}
```

