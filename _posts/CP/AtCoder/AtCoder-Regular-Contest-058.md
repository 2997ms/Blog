---
title: AtCoder Regular Contest 058
date: 2016-07-30 14:02:24
tags: [AtCoder,DP]
categories: 题记
---



C、D、E的代码在[这里](https://github.com/2997ms/My_Algorithm/tree/master/AtCoder/Regular_Contest_058)

这周一直在忙老师要求写的一些东西，终于在周六的时候还是光荣地倒下了，太累了。

原本是上周的比赛，自己一周的时间还是没有把F过掉，在Codeforces上面问了一些人，算是搞懂了原理，但是对于[yosupo](http://codeforces.com/profile/yosupo)所说的Z-algorithm还是没有学过，这个也加入到了[To_do_list](http://2997ms.github.io/2016/07/30/To-do-list/)里面，我一定会回来的。。。

## C Iroha's Obsession

--------



C题题意，找到一个数x，大于等于n，然后给出了k个数字是不能用的。

暴力就好了，一个一个往上面涨，判断是否符合条件，自己做法有些麻烦。



## D roha and a Grid

-------

D题题意是给出了一个长为H、宽为W的格子，要从左上角走到右下角，然后又给出了一块左下的区域，长为A、宽为B，不能到达，问最终的方法数。

这个问题在于要考虑这个蓝色的区域，枚举蓝色线上面的点每一个从左上角到达该点有多少种方法，然后考虑这些点都是竖着穿过下面的横线，计算下面的点到终点有多少种方式，相加即可。

起点(x,y)到终点(s,t)有c(x-s+y-t,x-s)种方法，算组合数就好了。

![R058_D](http://ob47badfj.bkt.clouddn.com/R058_D.png)



## E Iroha and Haiku
-------

E题题意是给出了N个数(N<=40)，每个数由1到10构成，问这些构成里面能找到相邻的三个子段，第一段和为X，第二段和为Y，第三段和为Z，满足这样条件的叫Haiku，问一共有多少个Haiku。

这个题好神。。。。

做法是找不符合的，把1写成1，把2写成10，把3写成100，那么最后的这段数就能写成一个01串，然后这个串包含xyz-Haiku，相当于与xyz构成的01串做与运算，结果不为0.



## F Iroha Loves Strings

--------



F题题意是给出了N个串，按照顺序组合为长度为k的串，保证可以找到。然后问字典序最小的字符串是多少。

问了yosupo，他是这么说的，大致看懂了，但是z-algorithm不会。。。

 ![R058_F_1](http://ob47badfj.bkt.clouddn.com/R058_F_1.png)

看了其他人AC的代码，有一些是水过去的，然后自己也按照思路写了一下。始终WA掉4个数据，不懂为什么啊啊啊。。。

求路过的人解释一下这道题啊。。。

WA代码：

```c++
//#pragma comment(linker, "/STACK:102400000,102400000") 
#pragma warning(disable:4996)
#include <fstream>
#include <iostream>
#include <functional>
#include <algorithm>
#include <cstring>
#include <vector>
#include <string>
#include <cstdio>
#include <cmath>
#include <queue>
#include <stack>
#include <deque>
#include <ctime>
#include <list>
#include <set>
#include <map>
using namespace std;
 
typedef long long ll;
#define eps 1e-10
#define LL_INF 0x33ffffffffffffff
#define INF 0x3f3f3f3f
#define mem(a, b) memset(a, b, sizeof(a))
#define pper(i,n,m) for(int i = n;i >= m; i--)
#define repp(i, n, m) for (int i = n; i <= m; i++)
#define rep(i, n, m) for (int i = n; i < m; i++)
#define sa(n) scanf("%d", &(n))
#define mp make_pair
#define ff first
#define ss second
#define pb push_back
 
const int maxn = 2e3 + 10;
const ll mod = 1e9 + 7;
const double PI = acos(-1.0);
ll po(ll a, ll b) { ll res = 1; a %= mod; for (; b; b >>= 1) { if (b & 1)res = res*a%mod; a = a*a%mod; }return res; }
 
int n, k;
string val[maxn];
int vis[maxn];
int dp[maxn][10010];
int loc[2][3005][2];
 
void solve()
{
	int i, j;
 
	set<char>ch;
	repp(i, 1, n)
	{
		cin >> val[i];
		rep(h, 0, val[i].length())
		{
			ch.insert(val[i][h]);
		}
	}
	mem(dp, 0);
	dp[n + 1][0] = 1;
 
	for (i = n; i >= 1; i--)
	{
		for (j = 0; j <= k; j++)
		{
			if (dp[i + 1][j])
			{
				dp[i][j] = 1;
			}
			if (dp[i + 1][j] && j + val[i].length() <= k )
			{
				dp[i][j + val[i].length()] = 1;
			}
		}
	}
	int now = 0, pre = 1;
	int cnt = 0;
	for (i = 1; i <= n; i++)
	{
		if (k - val[i].length()>=0 && dp[i + 1][k - val[i].length()])
		{
			cnt++;
			loc[pre][cnt][0] = i;
			loc[pre][cnt][1] = 0;
		}
	}
	string res = "";
	int remain = k;
	for (i = 1; i <= k; i++)
	{
		mem(vis, 0);
		char minx = 'z';
		for (j = 1; j <= cnt; j++)
		{
			minx = min(minx, val[loc[pre][j][0]][loc[pre][j][1]]);
		}
		int ncnt = 0;
		int mn = n;
		for (j = 1; j <= cnt; j++)
		{
			char x = val[loc[pre][j][0]][loc[pre][j][1]];
			if (x != minx)continue;
			if (loc[pre][j][1] == val[loc[pre][j][0]].length() - 1)
			{
				mn = min(mn, loc[pre][j][0]);
			}
			else
			{
				vis[loc[pre][j][0]] = 1;
				++ncnt;
				loc[now][ncnt][0] = loc[pre][j][0];
				loc[now][ncnt][1] = loc[pre][j][1] + 1;
			}
		}
		remain--;
		for (j = mn + 1; j <= n; j++)
		{
			if (vis[j])continue;
			if (remain - val[j].length() >= 0 && dp[j + 1][remain - val[j].length()])
			{
				ncnt++;
				loc[now][ncnt][0] = j;
				loc[now][ncnt][1] = 0;
			}
		}
		res = res + minx;
		cnt = ncnt;
		pre = 1 - pre;
		now = 1 - now;
	}
	cout << res << endl;
}
 
int main()
{
	while (scanf("%d%d", &n, &k) != EOF)
	{
		solve();
	}
	return 0;
}
```