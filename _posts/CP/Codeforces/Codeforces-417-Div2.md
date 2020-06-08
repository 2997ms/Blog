---
title: Codeforces 417 Div2
date: 2017-06-16 08:27:37
tags: [Codeforces,博弈]
categories: [题记]
---

[题目链接](http://codeforces.com/contest/812)

<!--more-->

### A Sagheer and Crossroads

> 每一个路口有交通灯，以及人行道，问给出的情况下会不会出现交通事故。

```C++
int val[50][50];
void solve()
{
	sa(val[1][4]);
	sa(val[1][3]);
	sa(val[1][2]);
	sa(val[1][1]);

	sa(val[2][1]);
	sa(val[2][4]);
	sa(val[2][3]);
	sa(val[2][2]);


	sa(val[3][2]);
	sa(val[3][1]);
	sa(val[3][4]);
	sa(val[3][3]);


	sa(val[4][3]);
	sa(val[4][2]);
	sa(val[4][1]);
	sa(val[4][4]);


	repp(i,1,4)
	{
		if(val[i][i])
		{
			repp(k,1,4)
			{
				if(i==k)continue;
				if(val[i][k])
				{
					puts("YES");
					return;
				}
			}
			repp(j,1,4)
			{
				if(i==j)continue;
				if(val[j][i])
				{
					//DE(j)
					//DE(i)
					puts("YES");
					return;
				}
			}
		}
	}
	puts("NO");
}
```



### B Sagheer, the Hausmeister

>一幢楼有左右两个楼梯，中间是房间。一个人站在一层的左楼梯上，现在要把这幢楼的所有房间的灯都关掉，问最少走多少距离。楼的层数$N<15$，每层楼的房间数$M<100$。

DP，做的时候有些麻烦，因为最终的位置不一定是最高层。题解直接是暴力选择$O(2^n\*n)$

```C++
int n, m;
char val[150][150];
int dp[150][2];

void solve()
{
	sa(n), sa(m);
	repp(i, 1, n)
	{
		scanf("%s", val[i] + 1);
	}
	//memset(dp,-1,sizeof(dp));
	int up = 0;
	repp(i, 1, n)
	{
		int flag = 0;
		for (int k = 1; k <= m + 2; k++)
		{
			if (val[i][k] == '1')
			{
				flag = 1;
			}
		}
		if (flag)
		{
			up = i;
			break;
		}
	}
	if (up == 0)
	{
		puts("0");
		return;
	}
	for (int i = n; i >= up; i--)
	{
		if (i == up)
		{
			int left = -1, right = -1;
			int cnt = 0;
			for (int k = 1; k <= m + 2; k++)
			{
				if (val[i][k] == '1')
				{
					right = k;
				}
			}
			for (int k = m + 2; k >= 1; k--)
			{
				if (val[i][k] == '1')
				{
					left = k;
				}
			}
			left = m + 2 - left;
			int res = dp[i + 1][0] + (right-1) + (up!=n);
			if(up!=n)res = min(res, dp[i + 1][1] + (left) + 1);
			printf("%d\n", res);
			return;
		}
		else if (i == n)
		{
			int p = 0;
			for (int k = 1; k <= m + 2; k++)
			{
				if (val[i][k] == '1')
				{
					p = k;
				}
			}
			if (p != 0)
			{
				dp[i][0] = (p - 1) * 2;
			}
			dp[i][1] = m + 1;
		}
		else
		{

			int left = -1, right = -1;
			int cnt = 0;
			for (int k = 1; k <= m + 2; k++)
			{
				if (val[i][k] == '1')
				{
					right = k;
				}
			}
			if (right == -1)
			{
				dp[i][0] = dp[i + 1][0] + 1;
				dp[i][0] = min(dp[i][0], dp[i + 1][1] + m + 2);
				dp[i][1] = dp[i + 1][1] + 1;
				dp[i][1] = min(dp[i][1], dp[i + 1][0] + m + 2);
				continue;
			}
			for (int k = m + 2; k >= 1; k--)
			{
				if (val[i][k] == '1')
				{
					left = k;
				}
			}
			left = m + 2 - left;
			dp[i][0] = dp[i + 1][0] + (right-1) * 2 + 1;
			dp[i][0] = min(dp[i][0], dp[i + 1][1] + m + 1 + 1);

			dp[i][1] = dp[i + 1][1] + (left) * 2 + 1;
			dp[i][1] = min(dp[i][1], dp[i + 1][0] + m + 1 + 1);
		}
	}
	puts("0");
}
```



### C Sagheer and Nubian Market

>有$n$个物品以及你拥有的总钱数$S$，现在每个物品的价钱是根据你最终购买物品的数量决定的，问最多你可以购买多少个。

二分。

```C++
ll n, S;
ll val[maxn];
ll v[maxn];
bool check(ll num)
{
	repp(i, 1, n)
	{
		v[i] = 1LL * val[i] + 1LL * i*num;
	}
	sort(v + 1, v + n + 1);

	int cnt = 0;
	ll sum = S;
	repp(i, 1, n)
	{
		if (sum >= v[i])
		{
			sum -= v[i];
			cnt++;
		}
		else
		{
			break;
		}
	}
	return cnt >= num;
}


void solve()
{
	scanf("%lld%lld", &n, &S);
	repp(i, 1, n)
	{
		scanf("%lld", &val[i]);
	}

	ll le = 0, ri = n;
	while (le<ri)
	{
		ll mid = (le + ri + 1) / 2;
		if (check(mid))
		{
			le = mid;
		}
		else
		{
			ri = mid - 1;
		}
	}
	printf("%lld ", le);
	if (le == 0)
	{
		printf("0");
		return;
	}
	repp(i, 1, n)
	{
		v[i] = 1LL * val[i] + 1LL * i*le;
	}
	sort(v + 1, v + n + 1);

	int cnt = 0;
	ll sum = S;
	repp(i, 1, le)
	{
		sum -= v[i];
	}
	printf("%lld", S - sum);
}
```



### D Sagheer and Kindergarten

> 题意是幼儿园有$n$个小朋友和$m$个玩具，小朋友会按照时间顺序提出请求玩哪一个玩具的请求，如果一个孩子得到了他所请求的所有玩具，那么他玩完会归还。否则他就继续等，也不会提出玩其他玩具的请求。如果有一些孩子永远无法得到他们请求的玩具，那么他们会哭。
>
> 现在给出前面的$k$个请求，保证不会有哭的情况。有$q$个询问，每个询问代表添加了最后的请求之后，计算有多少哭的孩子。

把每一个孩子当作点，当孩子等待另一个孩子的玩具的时候，连一条边。出现哭的情况即是出现了环。

每一个询问，就是看这两个点是否在一棵树中。用dfs序来判断是否是根节点与子节点的关系。

```C++
int n,m,k,q;
int fa[maxn],who[maxn];
int in[maxn],out[maxn];
vector<int>edge[maxn];
int idx;

void dfs(int x,int fa)
{
	idx++;
	in[x]=idx;
	for(int i=0;i<edge[x].size();i++)
	{
		int nxt=edge[x][i];
		if(nxt==fa)continue;
		dfs(nxt,x);
	}
	out[x]=idx;
}

void solve()
{
	sa(n),sa(m),sa(k),sa(q);
	repp(i,1,k)
	{
		int x,y;
		sa(x),sa(y);
		if(who[y])
		{
			fa[x]=who[y];
		}
		who[y]=x;
	}
	repp(i,1,n)
	{
		edge[fa[i]].push_back(i);
	}
	idx=0;
	repp(i,1,n)
	{
		if(in[i]==0)
		{
			dfs(i,-1);
		}
	}
	repp(i,1,q)
	{
		int x,y;
		sa(x),sa(y);
		y=who[y];
		if(in[x]<=in[y]&&out[x]>=out[y])
		{
			printf("%d\n", out[x]-in[x]+1);
		}
		else
		{
			puts("0");
		}
	}
}
```



### E Sagheer and Apple Tree

> 一颗树，每个节点上有$p\_i$数量的苹果，每个玩家有两种操作，1.吃掉叶子节点上任意数量的苹果。2.从父节点移到任意数量的苹果到其中一个子节点上。现在后手有一个机会去交换两个节点的数量，问后手有多少个交换节点的方案可以赢。

每个叶子节点的深度的奇偶性是一致的，阶梯博弈，只不过拿到了树上面。(如果不一致不知道怎么做了)。

然后就看最终和叶子节点深度的奇偶性一致的节点异或起来，为$0$后手胜。算方案数的话，枚举，加上另一个集合的对应数字的数量。

```C++

int ans,res;
int n;
int maxx;
int val[maxn];
int dep[maxn];
vector<int>edge[maxn];
map<int,int>cnt[2];
int c0,c1;

void dfs(int x)
{
	for(int i=0;i<edge[x].size();i++)
	{
		int nxt=edge[x][i];
		dfs(nxt);
		dep[x]=max(dep[x],dep[nxt]+1);
	}
	maxx=max(maxx,dep[x]);
	cnt[dep[x]&1][val[x]]++;
	if(dep[x]&1)c1++;
	else c0++;
}

void solve()
{
	sa(n);
	repp(i,1,n)
	{
		sa(val[i]);
	}
	repp(i,2,n)
	{
		int x;
		sa(x);
		edge[x].push_back(i);
	}
	maxx=0;
	dfs(1);

	for(int i=1;i<=n;i++)
	{
		if(dep[i]%2==0)
		{
			//DE(val[i])
			ans^=val[i];
		}
	}
	//DE(maxx)
	//DE(ans)
	ll res=0;
	if(ans==0)
	{
		res+=1LL*c0*(c0-1)/2;
		res+=1LL*c1*(c1-1)/2;
	}
	for(int i=1;i<=n;i++)
	{
		if(dep[i]%2==0)
		{
			int tmp=ans^val[i];
			res+=cnt[1][tmp];
		}

	}
	printf("%lld\n", res);
}
```

