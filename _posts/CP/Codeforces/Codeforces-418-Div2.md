---
title: Codeforces 418 Div2
date: 2017-06-16 09:24:32
tags: [Codeforces,DP,贪心]
categories: [题记]
---

[题目链接](http://codeforces.com/contest/814)

<!--more-->

### A An abandoned sentiment from past

```C++
int n,k;
int val[maxn],b[maxn];
void solve()
{
	sa(n),sa(k);
	if(k>1)
	{
		puts("Yes");
		return;
	}
	repp(i,1,n)
	{
		sa(val[i]);
	}
	repp(i,1,k)
	{
		sa(b[i]);
	}

	int left=-1,right=205;
	val[0]=-1;
	val[n+1]=205;
	int pre=-1;
	repp(i,1,n)
	{
		if(val[i]==0)
		{
			left=val[i-1];
			right=val[i+1];
			continue;
		}
		if(val[i]<=pre)
		{
			puts("Yes");
			return;
		}
		pre=val[i];
	}
	if(left<b[1]&&b[1]<right)
	{
		puts("No");
	}
	else
	{
		puts("Yes");
	}
}
```



### B An express train to reveries

> 给出两个序列$(n<1000)$，找出一个序列使得和这两个序列不同的元素的数量都恰好是一个。

```C++
int n;
int a[maxn], b[maxn];
int ans[maxn];
int vis[maxn];

void solve()
{
	sa(n);
	repp(i, 1, n)sa(a[i]);
	repp(i, 1, n)sa(b[i]);

	repp(i, 1, n)ans[i] = a[i];

	int p[2] = { 0,0 };
	repp(i, 1, n)
	{
		if (vis[a[i]] == 0)
		{
			vis[a[i]] = i;
		}
		else
		{
			p[0] = vis[a[i]];
			p[1] = i;
		}
	}

	repp(i, 0, 1)
	{
		int f = 0;
		repp(k, 1, n)
		{
			ans[p[i]] = k;
			memset(vis, 0, sizeof(vis));
			int flag = 0;
			repp(g, 1, n)
			{
				if (vis[ans[g]] == 0)
				{
					vis[ans[g]] = g;
				}
				else
				{
					flag = 1;
					break;
				}
			}
			if (flag)continue;
			repp(g, 1, n)
			{
				flag += (ans[g] != b[g]);
			}
			if (flag == 1)
			{
				f = 1;
				break;
			}
		}
		if (f)break;
		ans[p[i]] = a[p[i]];
	}
	repp(i, 1, n)
	{
		if (i>1)printf(" ");
		printf("%d", ans[i]);
	}
}
```



### C An impassioned circulation of affection

> 题意给出一个字符串(长度$n<1500$)，然后有$q(q<200000)$个询问$m\_i，c\_i$，每个询问是说可以修改字符串中的$m\_i$个字符，然后最长的连续$c\_i$是多少。

$n\*q$在$10^8$左右，发现了暴力的解法。。

自己用的预处理+dp，又是调了好久。。。好白痴。。。

```C++
int n;
char val[maxn];
int res[26][2000];
int dp[1501][1501];
int num[2000], pre[2000];

void solve()
{
	sa(n);
	scanf("%s", val + 1);
	repp(i, 2, n)
	{
		if (val[i] == val[i - 1])num[i] = num[i - 1] + 1;
		else num[i] = 1;
	}
	repp(v, 0, 25)
	{
		int va = 'a' + v;
		memset(dp, 0, sizeof(dp));//dp[pos][k] max_value

		repp(g, 1, n)
		{
			if (val[g] == va)
			{
				if (val[g - 1] == va)
				{
					dp[g][0] = dp[g - 1][0] + 1;
				}
				else
				{
					dp[g][0] = 1;
				}
			}
		}

		repp(k, 1, n)//number
		{
			res[v][k] = k;

			memset(pre, 0, sizeof(pre));

			int p = -1;
			repp(g, 1, n)
			{
				if (val[g] == va)
				{
					pre[g] = p;
					p = g;
				}
				else
				{
					pre[g] = p;
				}
			}
			repp(pos, 1, n)
			{
				dp[pos][k] = min(pos, k);
				res[v][k] = max(res[v][k], dp[pos][k]);
			}
			repp(pos, k + 1, n)
			{
				int dis = 0;
				if (pre[pos] == -1)
				{
					if (val[pos] == va)
					{
						dp[pos][k] = max(dp[pos][k], dp[pos - 1][k] + 1);
					}
					res[v][k] = max(res[v][k], dp[pos][k]);
					continue;
				}
				else
				{
					dis = pos - pre[pos];
					if (val[pos] == va)
					{
						dp[pos][k] = max(dp[pos][k], dp[pos - 1][k] + 1);
					}
					else
					{

						dp[pos][k] = max(dp[pos][k], dp[pos - 1][k - 1] + 1);
					}
				}
				res[v][k] = max(res[v][k], dp[pos][k]);
	
			}
		}
	}

	int q;
	sa(q);
	char x[5];
	repp(i, 1, q)
	{
		int tmp;
		scanf("%d%s", &tmp, x);
		//if(tmp>n)tmp=n;
		printf("%d\n", res[x[0] - 'a'][tmp]);
	}
}
```

而实际，预处理可以直接下面这么搞。。。

```C++
scanf("%d%s", &n, s + 1);
memset(ans, 0, sizeof(ans));
for(char ch = 'a'; ch <= 'z'; ch++)
{
  for(int i = 1; i <= n; i++)
  {
    int now = 0;
    for(int j = i; j <= n; j++)
    {
      now += (s[j] != ch);
      ans[ch - 'a'][now] = max(ans[ch - 'a'][now], j - i + 1);
    }
  }
  for(int i = 1; i <= n; i++)
    ans[ch - 'a'][i] = max(ans[ch - 'a'][i], ans[ch - 'a'][i - 1]);
}
inputnum(q);
int m;
char ch;
for(int i = 1; i <= q; i++)
{
  inputnum(m);
  ch = inputchar();
  while(ch < 'a' || ch > 'z')
    ch = inputchar();
  printf("%d\n", ans[ch - 'a'][m]);
}
```



### D An overnight dance in discotheque

> $n$个圆，要分两组。如果一个圆在一组里面被包含了偶数次，那么总面积要减掉该面积。问最大的总面积。

按照圆面积最大的开始贪心。

```C++
int n;
struct no
{
	int x, y, r;
	int id;
}node[maxn];

bool cmp(no &a, no &b)
{
	return a.r>b.r;
}
int c1, c2;
int val1[maxn], val2[maxn];

bool in(int a, int b)
{
	double dis = sqrt((1.0*node[a].x - node[b].x)*(1.0*node[a].x - node[b].x) + (1.0*node[a].y - node[b].y)*(1.0*node[a].y - node[b].y));
	double d = node[a].r;
	return d >= dis;
}

void solve()
{
	sa(n);
	repp(i, 1, n)
	{
		int x, y, r;
		sa(node[i].x);
		sa(node[i].y);
		sa(node[i].r);
		node[i].id = i;
	}
	sort(node + 1, node + n + 1, cmp);
	for (int i = 1; i <= n; i++)
	{
		int flag = 0;
		repp(k1, 1, c1)
		{
			if (in(val1[k1], i))
			{
				flag = 1;
			}
		}
		if (flag == 0)
		{
			c1++;
			val1[c1] = i;
			continue;
		}

		flag = 0;
		repp(k1, 1, c2)
		{
			if (in(val2[k1], i))
			{
				flag = 1;
			}
		}
		if (flag == 0)
		{
			c2++;
			val2[c2] = i;
		}
		else
		{
			c1++;
			val1[c1] = i;
		}
	}


	double ans = 0;
	repp(k, 1, c1)
	{
		int cnt = 0;
		for (int j = 1; j<k; j++)
		{
			if (in(val1[j], val1[k]))
			{
				cnt++;
			}
		}
		if (cnt & 1)ans -= PI*node[val1[k]].r*node[val1[k]].r;
		else ans += PI*node[val1[k]].r*node[val1[k]].r;
	}

	repp(k, 1, c2)
	{
		int cnt = 0;
		for (int j = 1; j<k; j++)
		{
			if (in(val2[j], val2[k]))
			{
				cnt++;
			}
		}
		if (cnt & 1)ans -= PI*node[val2[k]].r*node[val2[k]].r;
		else ans += PI*node[val2[k]].r*node[val2[k]].r;
	}
	printf("%.8lf\n", ans);
}
```



### E An unavoidable detour for home

> $n(n<50)$个点，每个点度是确定的($2$或者$3$)，现在需要构建一个树，以$1$为树根。要求：
>
> 1.每个点距离$1$的最短距离$len\_i$是确定的，并且该路径唯一。
>
> 2.对于$i>2$，满足$len\_i>len\_{i-1}$。

因为每个点距离树根是确定的而且唯一，满足上述的条件。所以想到按照层去枚举。

dp[a\][b\][c\][d\]表示上层有a个空着1条边的，b个空着2条边的，这一层有c个空着1条边的，d个空着2条边的。剩下的就是状态的转移了。。。

```C++
int n;
int d[55];
ll dp[2][55][55][55][55];

void solve()
{
	sa(n);
	repp(i, 1, n)
	{
		sa(d[i]);
	}
	dp[0][d[1] == 2][d[1] == 3][d[2] == 2][d[2] == 3] = 1;
	int now = 0, nxt = 1;
	for (int i = 2; i < n; i++)
	{
		for (int h = 0; h <= i; h++)
		{
			for (int j = 0; j + h <= i; j++)
			{
				for (int k = 0; k + j + h <= i; k++)
				{
					for (int le = 0; le + k + j + h <= i; le++)
					{
						if (dp[now][h][j][k][le] == 0)continue;
						if (h == 0 && j == 0)
						{
							if (k || le)
							{
								dp[now][k][le][0][0] += dp[now][h][j][k][le];
								dp[now][k][le][0][0] %= mod;
							}
							continue;
						}
						if (d[i + 1] == 2)
						{
							if (h)
							{
								dp[nxt][h - 1][j][k + 1][le] += dp[now][h][j][k][le] * h;
								dp[nxt][h - 1][j][k + 1][le] %= mod;

								if (k)
								{
									dp[nxt][h - 1][j][k - 1][le] += dp[now][h][j][k][le] * h*k%mod;
									dp[nxt][h - 1][j][k - 1][le] %= mod;
								}
								if (le)
								{
									dp[nxt][h - 1][j][k+1][le - 1] += dp[now][h][j][k][le] * h*le%mod;
									dp[nxt][h - 1][j][k+1][le - 1] %= mod;
								}
							}
							if (j)
							{
								dp[nxt][h + 1][j - 1][k + 1][le] += dp[now][h][j][k][le] * j;
								dp[nxt][h + 1][j - 1][k + 1][le] %= mod;

								if (k)
								{
									dp[nxt][h + 1][j - 1][k - 1][le] += dp[now][h][j][k][le] * j*k%mod;
									dp[nxt][h + 1][j - 1][k - 1][le] %= mod;
								}
								if (le)
								{
									dp[nxt][h + 1][j - 1][k + 1][le - 1] += dp[now][h][j][k][le] * j*le%mod;
									dp[nxt][h + 1][j - 1][k + 1][le - 1] %= mod;
								}
							}
						}
						else
						{
							if (h)
							{
								dp[nxt][h - 1][j][k][le + 1] += dp[now][h][j][k][le] * h%mod;
								dp[nxt][h - 1][j][k][le + 1] %= mod;

								if (k)
								{
									dp[nxt][h - 1][j][k][le] += dp[now][h][j][k][le] * h*k;
									dp[nxt][h - 1][j][k][le] %= mod;
								}
								if (le)
								{
									dp[nxt][h - 1][j][k + 2][le - 1] += dp[now][h][j][k][le] * h*le%mod;
									dp[nxt][h - 1][j][k + 2][le - 1] %= mod;
								}
								if (k >= 2)
								{
									dp[nxt][h - 1][j][k - 2][le] += dp[now][h][j][k][le] * h*k*(k - 1) / 2 % mod;
									dp[nxt][h - 1][j][k - 2][le] %= mod;
								}
								if (le >= 2)
								{
									dp[nxt][h - 1][j][k + 2][le - 2] += dp[now][h][j][k][le] * h*le*(le - 1) / 2 % mod;
									dp[nxt][h - 1][j][k + 2][le - 2] %= mod;
								}
								if (k&&le)
								{
									dp[nxt][h - 1][j][k][le - 1] += dp[now][h][j][k][le] * h*k*le;
									dp[nxt][h - 1][j][k][le - 1] %= mod;
								}
							}
							if (j)
							{
								dp[nxt][h+1][j - 1][k][le + 1] += dp[now][h][j][k][le] * j%mod;
								dp[nxt][h+1][j - 1][k][le + 1] %= mod;

								if (k)
								{
									dp[nxt][h+1][j - 1][k][le] += dp[now][h][j][k][le] * j*k%mod;
									dp[nxt][h+1][j-1][k][le] %= mod;
								}
								if (le)
								{
									dp[nxt][h+1][j - 1][k + 2][le - 1] += dp[now][h][j][k][le] * j*le%mod;
									dp[nxt][h+1][j-1][k + 2][le - 1] %= mod;
								}
								if (k >= 2)
								{
									dp[nxt][h+1][j - 1][k - 2][le] += dp[now][h][j][k][le] * j*k*(k - 1) / 2 % mod;
									dp[nxt][h+1][j - 1][k - 2][le] %= mod;
								}
								if (le >= 2)
								{
									dp[nxt][h + 1][j - 1][k + 2][le - 2] += dp[now][h][j][k][le] * j*le*(le - 1) / 2 % mod;
									dp[nxt][h + 1][j - 1][k + 2][le - 2] %= mod;
								}
								if (k&&le)
								{
									dp[nxt][h + 1][j - 1][k][le - 1] += dp[now][h][j][k][le] * j*k*le;
									dp[nxt][h + 1][j - 1][k][le - 1] %= mod;
								}
							}
						}
					}
				}
			}
		}
		swap(now, nxt);
		memset(dp[nxt], 0, sizeof(dp[nxt]));
	}

	printf("%lld\n", dp[now][0][0][0][0]);
}

```