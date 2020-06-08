---
title: University CodeSprint 3(待填)
date: 2017-10-04 22:23:10
tags: [Hackerrank, link-cut-tree]
categories: [待填]
---

十一的时候打的这个比赛，前五题感觉都很容易。然后第六题不会。。暴力骗了点分。。

后来看Editoral这个第六题确实没想到，学到了。。。但也不是很难啊。。。

第七题好像是link-cut-tree？没学过，以后补一下。

[题目链接](https://www.hackerrank.com/contests/university-codesprint-3/challenges)

<!--more-->

前三题太水了，不写了。

### 4.Bob's Game

> 题意是给出n*n的格子，然后有一些格子是到不了的，现在格子上有一堆的king，可以三种走法，向上、向左、向左上。走到最左上停止。问最终输赢情况，以及赢的第一步的方法数量。

因为每一个king都是独立的，就算每一个king的sg值，异或起来。然后O(3n)去check一遍。

```C++
const int dx3[5] = { 0,-1,0,-1,0 };
const int dy3[5] = { 0,0,-1,-1,0 };
int sg(set<int>&v)
{
	repp(i, 0, 4)
	{
		if (v.find(i) == v.end())return i;
	}
	return -1;
}

int n;
char val[1005][1005];
int dp[1005][1005];

void solve()
{
	memset(dp, 0, sizeof(dp));
	sa(n);
	repp(i, 1, n)
	{
		scanf("%s", val[i] + 1);
		repp(j, 1, n)
		{
			if (i == 1 && j == 1)
			{
				dp[i][j] = 0;
				continue;
			}
			set<int>s;
			repp(k, 1, 3)
			{
				int nx = i + dx3[k];
				int ny = j + dy3[k];
				if (nx<1 || ny<1 || val[nx][ny] == 'X')continue;
				s.insert(dp[nx][ny]);
			}
			dp[i][j] = sg(s);
		}
	}
	int ans = 0;
	repp(i, 1, n)
	{
		repp(j, 1, n)
		{
			if (val[i][j] == 'K')ans ^= dp[i][j];
		}
	}
	if (ans)
	{
		printf("WIN ");
		int res = 0;
		repp(i, 1, n)
		{
			repp(j, 1, n)
			{
				if (val[i][j] == 'K')
				{
					int tmp = ans;
					tmp ^= dp[i][j];
					repp(k, 1, 3)
					{
						int nx = i + dx3[k];
						int ny = j + dy3[k];
						if (nx<1 || ny<1 || val[nx][ny] == 'X')continue;
						int t = tmp ^ dp[nx][ny];
						if (t == 0)res++;
					}
				}
			}
		}
		printf("%d\n", res);
	}
	else
	{
		puts("LOSE");
	}
}
```



### 5.Black White Tree

>题意是给出一颗黑白树，每个点要么黑要么白，现在你需要切割一刻子树，让这个子树的黑点数量与白点数量差值的绝对值最大，求最大值以及选点的情况。

黑点为1，白点为-1。子树的和即为差值。

dp[x\][1]表示选这个点差值的最大值。dp[x\][0]表示不选这个点差值的最大值。

求出哪一个分支差值最大之后，以该点为根，重新搜，然后贪心取。

```C++
int n;
int col[maxn];
vector<int> edge[maxn];
int dp[maxn][2], dp2[maxn][2];

vector<int>res1;
vector<int>res2;
void dfs(int x, int fa)
{
	dp[x][1] = col[x];

	dp2[x][1] = (col[x] == 1 ? -1 : 1);
	for (int i = 0; i<edge[x].size(); i++)
	{
		int nxt = edge[x][i];
		if (nxt == fa)continue;
		dfs(nxt, x);
		dp[x][1] += (dp[nxt][1]>0 ? dp[nxt][1] : 0);
		dp[x][0] = max(dp[x][0], dp[nxt][1]);
		dp[x][0] = max(dp[x][0], dp[nxt][0]);

		dp2[x][1] += (dp2[nxt][1]>0 ? dp2[nxt][1] : 0);
		dp2[x][0] = max(dp2[x][0], dp2[nxt][1]);
		dp2[x][0] = max(dp2[x][0], dp2[nxt][0]);
	}
	//if(dp[x][1]>0)res1.push_back(x);
	//if(dp2[x][1]>0)res2.push_back(x);
}

void dfs21(int x, int fa)
{
	res1.push_back(x);
	for (int i = 0; i<edge[x].size(); i++)
	{
		int nxt = edge[x][i];
		if (nxt == fa)continue;
		if (dp[nxt][1] <= 0)continue;
		dfs21(nxt, x);
	}
	//if(dp[x][1]>0)res1.push_back(x);
	//if(dp2[x][1]>0)res2.push_back(x);
}

void dfs22(int x, int fa)
{
	res2.push_back(x);
	for (int i = 0; i<edge[x].size(); i++)
	{
		int nxt = edge[x][i];
		if (nxt == fa)continue;
		if (dp2[nxt][1] <= 0)continue;
		dfs22(nxt, x);
	}
	//if(dp[x][1]>0)res1.push_back(x);
	//if(dp2[x][1]>0)res2.push_back(x);
}

void solve()
{
	sa(n);
	repp(i, 1, n)
	{
		sa(col[i]);
		if (col[i] == 0)col[i]--;
	}
	repp(i, 1, n - 1)
	{
		int x; int y;
		sa(x), sa(y);
		edge[x].push_back(y);
		edge[y].push_back(x);
	}
	int ans1 = 0, ans2 = 0;
	int p1 = 1, p2 = 1;
	dfs(1, -1);
	repp(i, 1, n)
	{
		if (dp[i][1]>ans1)
		{
			ans1 = dp[i][1];
			p1 = i;
		}
		if (dp2[i][1]>ans2)
		{
			ans2 = dp2[i][1];
			p2 = i;
		}
	}
	//DE(ans1)
	//DE(ans2)
	memset(dp, 0, sizeof(dp));
	memset(dp2, 0, sizeof(dp2));
	if (ans1>ans2)
	{
		dfs(p1, -1);
		dfs21(p1, -1);
		cout << ans1 << endl;
		cout << res1.size() << endl;
		rep(i, 0, res1.size())
		{
			if (i)cout << " ";
			cout << res1[i];
		}
		cout << endl;
	}
	else
	{
		dfs(p2, -1);
		dfs22(p2, -1);
		cout << ans2 << endl;
		cout << res2.size() << endl;
		rep(i, 0, res2.size())
		{
			if (i)cout << " ";
			cout << res2[i];
		}
		cout << endl;
	}
}
```

### 6.March of the King

> 题意是在8*8的棋盘上找一个长度为11的字符串，格子不可以重复走。问方案数量。

$7^{11}$复杂度不够啊。。。

然而之前和别人讨论过的啊，一半一半查找，然后记录路径做哈希，求的时候容斥求和。



### 7.Simple Tree Counting(待补)





