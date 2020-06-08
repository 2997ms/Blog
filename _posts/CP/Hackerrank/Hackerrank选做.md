---
title: Hackerrank选做
date: 2019-08-04 16:46:49
tags: [Hackerrank, DP]
categories: [题记]
---

> 如果不坚持对现实表示些不满、刻薄或者绝望的话, 人生就虚伪得连一点诚意都没有了.

Create at: 2017-10-06 14:35:49

<!--more-->

### [Kingdom Division](https://www.hackerrank.com/challenges/kingdom-division)

>题意是给出一棵树，然后要对这棵树上的每一个点进行0或者1的染色，要求不能一个点的相邻节点都是另一个颜色，这样分配会引发战争。问有多少种染色方法。

dp[x\][0/1\][0/1\]表示该x点为颜色0/1时周围是否全是其他颜色的点。0代表孤立无援，1代表有相邻的相同颜色的点了。

转移的话，想着时刻维护合法性。相邻为1的是总数-孤立无援的情况。

```C++
int n;
vector<int>edge[maxn];
ll dp[maxn][2][2];
// col 0 1
// 0: out of limb
// 1: has neighbor

void hmod(ll & x)
{
	x = (x%mod+mod)%mod;
}

void dfs(int x,int fa)
{
	int cnt = 0;
	for(int i=0;i<edge[x].size();i++)
	{
		int nxt = edge[x][i];
		if(nxt == fa)continue;
		dfs(nxt,x);
		cnt++;
	}
	if(cnt==0)
	{
		dp[x][0][0]=1;
		dp[x][1][0]=1;
		return;
	}
	ll sum1 = 1,sum2 = 1;
	dp[x][0][0] = dp[x][1][0] = 1;
	for(int i=0;i<edge[x].size();i++)
	{
		int nxt = edge[x][i];
		if(nxt == fa)continue;
		dp[x][0][0] = dp[x][0][0]*dp[nxt][1][1];
		hmod(dp[x][0][0]);

		dp[x][1][0] = dp[x][1][0]*dp[nxt][0][1];
		hmod(dp[x][1][0]);

		sum1 = sum1*(dp[nxt][1][1]+dp[nxt][0][0]+dp[nxt][0][1]);
		hmod(sum1);

		sum2 = sum2*(dp[nxt][1][1]+dp[nxt][1][0]+dp[nxt][0][1]);
		hmod(sum2);
	}
	dp[x][0][1] = sum1 - dp[x][0][0];
	hmod(dp[x][0][1]);

	dp[x][1][1] = sum2 - dp[x][1][0];
	hmod(dp[x][1][1]);
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
	dfs(1, -1);
	ll ans = (dp[1][0][1] + dp[1][1][1]) % mod;
	printf("%lld\n", ans);
}
```



### [Prime XOR](https://www.hackerrank.com/challenges/prime-xor)

> 有100000个数，每个数的范围在[3500,4500]，然后问这些数里面可以组成多少个集合，满足这个集合里面的数的异或是一个质数。

观察到每一个数的范围就在[3500,4500]，所以只需记录数量，然后01背包。考虑数量上的转移即可。

```C++
int vis[maxn];

void init()
{
	int up = 1e4;
	for(int i=2;i<=up;i++)
	{
		if(vis[i])continue;
		//if(i>=2048&&i<=8192)cout<<i<<endl;
		for(int j=i+i;j<=up;j+=i)
		{
			vis[j]=1;
		}
	}
}

int n;
int val[maxn];
int dp[1005][8200];
void solve()
{
	sa(n);
	memset(val,0,sizeof(val));
	repp(i,1,n)
	{
		int x;
		sa(x);
		val[x]++;
	}
	dp[0][0]=1;
	repp(k,3500,4500)
	{
		int i = k-3500+1;
		for(int pre=0;pre<=8192;pre++)
		{
			dp[i][pre] = 1LL*(val[k]/2+1)*dp[i-1][pre]%mod;
		}
		for(int pre=0;pre<=8192;pre++)
		{
			dp[i][pre^k] += 1LL*(val[k]+1)/2*dp[i-1][pre]%mod;
			while(dp[i][pre^k]>=mod)dp[i][pre^k]-=mod;
		}
	}
	int ans=0;
	for(int i=2;i<=8192;i++)
	{
		if(vis[i]==0)
		{
			ans+=dp[1001][i];
			ans%=mod;
		}
	}
	cout<<ans<<endl;
}
```



### [Fair Cut](https://www.hackerrank.com/challenges/fair-cut/problem)

> 题意是从n个数中选出k个作为一个集合，剩下的数字作为一个集合，需要使两个集合中的
> $$
> f(I)=\sum\_{i\in{I}}\sum\_{j\in{J}}|a\_i-a\_j|
> $$
> 最小，问最小值。

一开始也想到dp，但是不知道怎么处理绝对值。。。

应该想到。。排序，然后可以确定地知道每一个数的贡献了。

```c++
ll n,k;
ll dp[3005][3005];
ll val[3005]; 

void solve()
{
	scanf("%lld%lld",&n,&k);
	repp(i,1,n)
	{
		scanf("%lld",&val[i]);
	}
	sort(val+1,val+n+1);
	memset(dp,0x3f,sizeof(dp));

	dp[0][0]=0;
	repp(i,1,n)
	{
		repp(j,0,i-1)
		{
			ll t1 = dp[i-1][j] + val[i]*(i-1 -j-(n-k-(i-1 -j)));
			dp[i][j+1]=min(dp[i][j+1],t1);

			ll t2 = dp[i-1][j] + (val[i]*(j-(k-j)));
			dp[i][j]=min(dp[i][j],t2);
		}
	}
	printf("%lld\n", dp[n][k]);
}
```

### [Find the Running Median](https://www.hackerrank.com/challenges/find-the-running-median/problem)

> 被set搞死，multiset搞定。
>
> 看Solution其实是priority_queue

```c++
vector<double> runningMedian(vector<int> a) {
  vector<double>ans;
  multiset<double>small;
  multiset<double>big;
  double med = 0;
  for(int i=0;i<a.size();i++) {
    if(i==0) {
        big.insert(a[i]);
    } else {
        if(a[i]>=med){
            big.insert(a[i]);
        } else {
            small.insert(a[i]);
        }
    }

    while(big.size() > small.size() + 1) {
      int x = *big.begin();
      big.erase(big.begin());
      small.insert(x);
    }
    while(small.size() > big.size() + 1) {
      auto g = small.end();
      g--;
      big.insert(*g);
      small.erase(g);
    }
    int sz1 = small.size();
    int sz2 = big.size();
    if((sz1 + sz2) % 2) {
      if(big.size() > small.size())
       {
           med = (*big.begin());
        ans.push_back(1.0*(*big.begin()));
       }
      else {
        auto g = small.end();
        g--;
        med = (*g);
        ans.push_back(1.0*(*g));
      }
    } else {
      auto g = small.end();
      g--;
      med = 1.0*(*g + 1.0*(*big.begin()))/2.0;
      ans.push_back(med);
    }

  }
  return ans;
}
```

Other Solution:

```c++
priority_queue<int, vector<int>, greater <int> > min_heap;
priority_queue<int> max_heap;
void add(int a)
{
    if( max_heap.size() && a >= max_heap.top())
        min_heap.push(a);
    else
        max_heap.push(a);

    if(abs(max_heap.size() - min_heap.size()) > 1)
    {
        if(max_heap.size() > min_heap.size())
        {
            int temp = max_heap.top();
            max_heap.pop();
            min_heap.push(temp);
        }
        else
        {
            int temp = min_heap.top();
            min_heap.pop();
            max_heap.push(temp);
        }
    }
}
```