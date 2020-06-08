---
title: Codeforces 416 Div2
date: 2017-05-30 13:06:20
tags: [Codeforces, DP, 并查集, 线段树]
categories: [题记]
---

Codeforces怎么就都到416了啊，好快。

简单记录一下这次Div2。

[题目链接](http://codeforces.com/contest/811)

<!--more-->

### A. Vladik and Courtesy

> 按题意模拟一下就好了。

```C++
int a,b;
void solve()
{
	cin>>a>>b;

	int cnt=1;
	int sum=1;
	while(true)
	{
		if(cnt)
		{
			if(a>=sum)
			{
				a-=sum;
			}
			else
			{
				puts("Vladik");
				return;
			}
		}
		else
		{
			if(b>=sum)
			{
				b-=sum;
			}
			else
			{
				puts("Valera");
				return;
			}
		}
		sum++;
		cnt=1-cnt;
	}
}
```

### B.Vladik and Complicated Book

> 给出$10^4$个数，$10^4$个询问，每次询问是该区间排序之后特定位置的数字是否改变。

sort会超时，计数排序，或者算大小关系。

```C++
int n, m;
int val[maxn];
int cnt[maxn];
int v[maxn];

void solve()
{
	sa(n), sa(m);
	repp(i, 1, n)
	{
		sa(val[i]);
	}
	repp(i, 1, m)
	{
		int le, ri, x;
		sa(le), sa(ri), sa(x);

		int minn = 1e5, maxx = 0;
		repp(k, le, ri)
		{
			cnt[val[k]]++;
			maxx = max(maxx, val[k]);
			minn = min(minn, val[k]);
		}
		int st = minn;
		repp(i, le, ri)
		{
			while (cnt[st] == 0)st++;
			cnt[st]--;
			v[i] = st;
		}
		if (v[x] == val[x])
		{
			puts("Yes");
		}
		else
		{
			puts("No");
		}
	}
}
```

### C.Vladik and Memorable Trip

> $n(n<5000)$个数，每次选择一个数字要把该数字包含的区间都选择上，问所有区间的异或和最大是多少。

算每个数字的区间，然后dp。

```C++
dp[ri] = max(dp[ri], dp[le - 1] + sum);
dp[i] = max(dp[i], dp[i - 1]);
```

至于算每个数字的区间，要不断把这个区间扩大，直到区间不再变大为止。

```C++
int n;
int val[maxn];
int vis[maxn];
ll dp[maxn];

int rig[maxn], lef[maxn];

void solve()
{
	sa(n);
	repp(i, 1, n)
	{
		sa(val[i]);
	}
	repp(i, 1, n)
	{
		int le = i;
		int ri = i;
		repp(k, 1, n)
		{
			if (val[i] == val[k])
			{
				le = min(le, k);
				ri = max(ri, k);
			}
		}
		rig[val[i]] = ri;
		lef[val[i]] = le;
	}

	repp(i, 1, n)
	{
		int le = lef[val[i]];
		int ri = rig[val[i]];

		int newle = le;
		int newri = ri;
		repp(k, le, ri)
		{
			newle = min(newle, lef[val[k]]);
			newri = max(newri, rig[val[k]]);
		}
		if (newle == le&&newle == ri)
		{

		}
		else
		{
			while (true)
			{
				int ple = le, pri = ri;
				le = newle, ri = newri;
				repp(k, newle, ple)
				{
					newle = min(newle, lef[val[k]]);
					newri = max(newri, rig[val[k]]);
				}

				repp(k, pri, newri)
				{
					newle = min(newle, lef[val[k]]);
					newri = max(newri, rig[val[k]]);
				}
				if (newle == le&&newri == ri)
				{
					break;
				}
				else
				{
					le = newle;
					ri = newri;
				}
			}
		}

		int sum = 0;
		repp(k, le, ri)
		{
			if (vis[val[k]])continue;
			sum ^= val[k];
			vis[val[k]] = 1;
		}
		dp[ri] = max(dp[ri], dp[le - 1] + sum);
		dp[i] = max(dp[i], dp[i - 1]);
		repp(k, le, ri)
		{
			vis[val[k]] = 0;
		}
	}
	ll ans = 0;
	repp(i, 1, n)
	{
		ans = max(ans, dp[i]);
	}
	printf("%lld\n", ans);
}
```

### D. Vladik and Favorite Game

> 交互题，给定起点是$(1,1)$，给定一个终点，现在你去操作这个机器人走到终点，其中，方向键“Up”“Down”可能是相反的，“Left”“Right”也有可能是相反的。地图中有陷阱，走到陷阱游戏算输。

因为起点在$(1,1)$，是在左上角，所以肯定可以直接测出一种方向键，之后在走的时候也一定可以测出另一个方向键，因为是在边缘上。所以直接bfs，dfs各一遍就好了。

```C++
int n, m;
char val[maxn][maxn];
int step[maxn][maxn];
char v[5] = { ' ','U','R','D','L' };

void bfs(int x,int y)
{
	memset(step, -1, sizeof(step));
	queue< pair<int, int> > que;
	que.push(mp(x, y));
	step[x][y] = n*m;
	while (!que.empty())
	{
		pair<int, int> now = que.front();
		que.pop();

		int x = now.first;
		int y = now.second;

		repp(k, 1, 4)
		{
			int nx = x + dx4[k];
			int ny = y + dy4[k];
			if ((val[nx][ny] == '.')&& step[nx][ny] == -1)
			{
				step[nx][ny] = step[x][y] - 1;
				que.push(mp(nx, ny));
			}
		}
	}
}

void dfs(int x, int y)
{
	if (val[x][y] == 'F')return;
	int dir = 1;
	int px, py;
	repp(k, 1, 4)
	{
		int nx = x + dx4[k];
		int ny = y + dy4[k];
		if (step[nx][ny] == step[x][y] + 1)
		{
			px = nx;
			py = ny;
			dir = k;
			break;
		}
	}
	printf("%c\n", v[dir]);
	fflush(stdout);

	int newx, newy;
	sa(newx), sa(newy);
	if (newx == px && newy == py)
	{
		dfs(newx, newy);
	}
	else
	{
		if (dir == 1 || dir == 3)
		{
			swap(v[1], v[3]);
		}
		else
		{
			swap(v[2], v[4]);
		}
		dfs(x, y);
	}
}

void solve()
{
	sa(n), sa(m);
	repp(i, 1, n)
	{
		scanf("%s", val[i] + 1);
	}
	int px = 1, py = 1;
	repp(i, 1, n)
	{
		repp(j, 1, m)
		{
			if (val[i][j] == 'F')
			{
				px = i;
				py = j;
			}
		}
	}
	bfs(px, py);
	dfs(1, 1);
}

```

### E.Vladik and Entertaining Flags

> 题意是给出一个$n\*m(n<10,m<10^5)$的格子，里面有数字，然后有$q$个询问，每次询问是地图从上到下的一块区域，问有多少个数字块。

不会做。。。

发现是线段树里面套的并查集，因为$n<10$，区间可以记录两边的数字所属的集合，合并的时候左边区间的右端和右边区间的左端颜色相同的进行合并。

```C++
int n, m, q;
int val[15][maxn];

struct DSU
{
	int fa[maxn];
	void init(int n)
	{
		for (int i = 0; i <= n; i++)
		{
			fa[i] = i;
		}
	}
	int find(int x)
	{
		return fa[x] == x ? x : fa[x] = find(fa[x]);
	}
	int uni(int x, int y)
	{
		x = find(x);
		y = find(y);
		if (x == y)return 0;
		if (x<y)swap(x, y);
		fa[x] = y;
		return 1;
	}
}dsu;
vector<int>tmp[42];
struct node
{
	int le, ri;
	int ans;
	int col[55];
	void init()
	{
		ans = 2 * n;
		dsu.init(2 * n);
		for (int i = 1; i <= n; i++)
		{
			ans -= dsu.uni(i, i + n);
		}
		for (int i = 2; i <= n; i++)
		{
			if (val[i][le] == val[i-1][le])
			{
				ans -= dsu.uni(i, i - 1);
			}
		}
		for (int i = 1; i <= n; i++)
		{
			col[i] = col[i + n] = dsu.find(i);
		}
	}
	node operator +(const node &other)
	{
		node res;
		res.le = le;
		res.ri = other.ri;

		res.ans = ans + other.ans;

		for (int i = 1; i <= n; i++)
		{
			dsu.fa[i] = col[i];
			dsu.fa[i + n] = col[i + n];
			dsu.fa[i + 2 * n] = other.col[i] + 2 * n;
			dsu.fa[i + 3 * n] = other.col[i + n] + 2 * n;
		}

		for (int i = 1; i <= n; i++)
		{
			if (val[i][ri] == val[i][other.le])
			{
				res.ans -= dsu.uni(i + n, i + 2 * n);
			}
		}
		for (int i = 0; i <= 40; i++)
		{
			tmp[i].clear();
		}
		for (int i = 1; i <= n; i++)
		{
			tmp[dsu.find(i)].push_back(i);
			tmp[dsu.find(i + 3 * n)].push_back(i + n);
		}
		for (int i = 0; i <= 40; i++)
		{
			for (int k = 0; k < tmp[i].size(); k++)
			{
				res.col[tmp[i][k]] = tmp[i][0];
			}
		}
		return res;
	}
}no[4 * maxn];

void build(int root, int le, int ri)
{
	if (le == ri)
	{
		no[root].le = no[root].ri = le;
		no[root].init();
		return;
	}
	int mid = (ri + le) / 2;
	build(root << 1, le, mid);
	build(root << 1 | 1, mid + 1, ri);
	no[root] = no[root << 1] + no[root << 1 | 1];
}

node query(int root, int le, int ri)
{
	if (no[root].le == le&&no[root].ri == ri)
	{
		return no[root];
	}
	int mid = (no[root].le + no[root].ri) / 2;
	if (ri <= mid)return query(root << 1, le, ri);
	if (le > mid)return query(root << 1 | 1, le, ri);
	return query(root << 1, le, mid) + query(root << 1 | 1, mid + 1, ri);
}


void solve()
{
	sa(n), sa(m), sa(q);
	for (int i = 1; i <= n; i++)
	{
		for (int j = 1; j <= m; j++)
		{
			scanf("%d", &val[i][j]);
		}
	}
	build(1, 1, m);
	repp(i, 1, q)
	{
		int x, y;
		sa(x), sa(y);
		node res = query(1, x, y);
		printf("%d\n", res.ans);
	}
}

int main()
{

	solve();
	//system("pause");
	return 0;
}
```

