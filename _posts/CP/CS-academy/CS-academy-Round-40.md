---
title: CS academy Round 40
date: 2017-08-05 15:42:35
tags: [CS academy]
categories: [题记]
---

似乎。。。要锻炼一下了啊。。。

<!--more-->

[题目链接](https://csacademy.com/contest/round-40/summary/)

### A Erase Value

```c++
int n;
int val[maxn];

void solve()
{
	sa(n);
	
	int sum = 0;
	repp(i,1,n)
	{
		sa(val[i]);
		sum+=val[i];
	}
	repp(i,1,n)
	{
		int cnt=0;
		repp(j,1,n)
		{
			if(val[i]==val[j])continue;
			cnt+=val[j];
		}
		sum=min(sum,cnt);
	}
	printf("%d\n", sum);
}
```



### B Move the Bishop

宽搜一发。

```C++
int r1,c1,r2,c2;
int val[10][10];

void solve()
{
	sa(r1),sa(c1),sa(r2),sa(c2);
	memset(val,-1,sizeof(val));

	queue< pair<int,int> >que;
	que.push(mp(r1,c1));
	val[r1][c1] = 0;
	while(!que.empty())
	{
		auto now = que.front();
		que.pop();
		int x = now.first;
		int y = now.second;
		repp(k,1,4)
		{
			repp(step,1,10)
			{
				int nx = x + step*dx44[k];
				int ny = y + step*dy44[k];
				if(nx>=1&&nx<=8&&ny>=1&&ny<=8)
				{
					if(val[nx][ny]==-1||val[nx][ny]>val[x][y]+1)
					{
						val[nx][ny]=val[x][y]+1;
						que.push(mp(nx,ny));
					}
				}
			}
		}
	}

	printf("%d\n", val[r2][c2]);
}
```



### C Switch the Lights

> 题意是给出了$n$个灯的起始状态，然后每一个位置有一个开关，可以翻转$[i,r_i]$，代价是$c_i$，问最终的最小代价，如果不符合题意则输出$-1$。

仔细思考啊。。。会发现解法是固定的，从$1$到$n$扫过去，如果位置$i$是开着的，那么是一定需要翻转$[i,r_i]$，就这么一直记录状态就好了。

```C++
int n;
char val[maxn];
int v[maxn];
int r[maxn],c[maxn];

void solve()
{
	sa(n);
	scanf("%s",val+1);
	repp(i,1,n)
	{
		sa(r[i]);
	}
	repp(i,1,n)
	{
		sa(c[i]);
	}
	ll cost = 0;
	int state = 0;
	repp(i,1,n)
	{
		state ^= v[i];
		if(state ^(val[i]-'0'))
		{
			state ^= 1;
			cost += c[i];
			v[r[i]+1] ^= 1;
		}
	}
	printf("%lld\n", cost);
}
```



### D Restricted Permutations

> $n$个数字，给出了$i$到$i+1$的位置关系，求有多少种排列方式。

dp。考虑x个数字，x在位置1到x有多少种，然后根据x与x+1的位置关系转移。

```C++
int n;
int val[2005];

vector<int>PlaceBefore(vector<int>&val)
{
	int n = val.size();
	vector<int>next(n + 1, 0);

	int sum = 0;
	for (int i = n - 1; i >= 0; i--)
	{
		sum += val[i];
		sum %= mod;
		next[i] = sum;
	}
	return next;
}

vector<int>PlaceAfter(vector<int>&val)
{
	int n = val.size();
	vector<int>next(n + 1, 0);

	int sum = 0;
	for (int i = 1; i <= n; i++)
	{
		sum += val[i - 1];
		sum %= mod;
		next[i] = sum;
	}
	return next;
}

void solve()
{
	sa(n);
	repp(i, 2, n)
	{
		sa(val[i]);
	}
	vector<int>pos = { 1 };
	repp(i, 2, n)
	{
		if (val[i])
		{
			pos = PlaceAfter(pos);
		}
		else
		{
			pos = PlaceBefore(pos);
		}
	}

	int sum = 0;
	for (auto itr : pos)
	{
		sum += itr;
		sum %= mod;
	}
	printf("%d\n", sum);
}

```



### E Direct the Graph

> 当前是一个$n$个点的无向完全图，现在要你对每一个边标记方向，有最多的3元环。问最多的3元环个数以及方案。

答案即是：所有的环 - bad环。

所有的环：C(n,3)。

bad环：任意一个点的两个同方向的路径都会组成一个bad环。

```C++
ll n;
void solve()
{
	cin>>n;
	ll ans = n*(n-1)*(n-2)/6;
	ll bad = (n-1)/2;
	bad = (bad*(bad-1)/2+(n-1-bad)*(n-1-bad-1)/2);
	bad = n*bad/2;
	ans = ((ans-bad)%mod+mod)%mod;
	cout<<ans<<endl;
	ll cnt = (n-1)/2;
	for (int i=n-1;i>=0;i--)
	{
		string c1(i-cnt,'0');
		string c2(cnt,'1');

		cnt-=1;
		cnt=max(cnt,0ll);
		cout<<c1<<c2;
	}
}
```