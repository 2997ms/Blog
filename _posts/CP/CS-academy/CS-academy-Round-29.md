---
title: 'CS academy Round 29'
date: 2017-05-30 22:27:46
tags: [CS academy,树形DP]
categories: [题记]
---

终于把E的树形DP搞明白了.

[题目链接](https://csacademy.com/contest/round-29/tasks/)

<!--more-->

### A.Missing Number

> 数组里找最小没有出现的正数。简单题不表。

### B.Positive Xors

> 给出一组数，找到最大的子数组，其异或不为0。

从前往后扫一遍，从后往前扫一遍。

```C++
int n;
int val[maxn];
int pre[maxn],suf[maxn];

void solve()
{
	sa(n);
	repp(i,1,n)
	{
		sa(val[i]);
	}
	int res=0;
	int cur=0;
	repp(i,1,n)
	{
		cur^=val[i];
		if(cur)res=max(res,i);
	}
	cur=0;
	for(int i=n;i>=1;i--)
	{
		cur^=val[i];
		if(cur)
		{
			res=max(res,n-i+1);
		}
	}
	cout<<res<<endl;
}

```

### C.Equality

> 题意是给出一组数，有一种操作是可以选择两个数$A[i\]，A[j\]$，对其中一个$+1$，另一个$-1$，问最少操作多少次，可以使得这些数的$Max-Min<=K$。

暴力。。。记录最大值，最小值的个数与位置。

```C++
int n,k;
int val[maxn];
int hax[maxn];
void solve()
{
	sa(n),sa(k);
	int maxx=0,minn=1e5;
	repp(i,1,n)
	{
		sa(val[i]);
		hax[val[i]]++;
		minn=min(minn,val[i]);
		maxx=max(maxx,val[i]);
	}
	int cnt=0;
	while(true)
	{
		if(abs(maxx-minn)<=k)break;
		int num=min(hax[minn],hax[maxx]);
		cnt+=num;
		//DE(maxx)
		//DE(minn)
		hax[minn]-=num;
		hax[maxx]-=num;
		if(hax[minn]==0)
		{
			minn++;
			hax[minn]+=num;
		}
		else
		{
			hax[minn+1]+=num;
		}
		if(hax[maxx]==0)
		{
			maxx--;
			hax[maxx]+=num;
		}
		else
		{
			hax[maxx-1]+=num;
		}
	}
	cout<<cnt<<endl;
}

```

### D.Odd Palindromes

>一个字符串是odd palindromic的意思是它的所有奇数的子串都是回文串。现在给出一个字符串，最多可以修改$K$次，问可以达到的最长的odd palindromic长度是多少。

发现odd palindromic就是ABABA...这种形式的。

然后最多修改$K$次，可以分别记录一下奇数位置与偶数位置的值，双指针搞。$O(n)$。

```C++
int k;
char val[maxn];
int num[2][30];

bool check()
{
	int sum1 = 0, sum2 = 0;
	int maxx1 = 0, maxx2 = 0;

	for (int i = 0; i<26; i++)
	{
		sum1 += num[0][i];
		maxx1 = max(maxx1, num[0][i]);
	}

	int sur = k - (sum1 - maxx1);


	for (int i = 0; i<26; i++)
	{
		sum2 += num[1][i];
		maxx2 = max(maxx2, num[1][i]);
	}
	sur = sur - (sum2 - maxx2);

	if (sur<0)return false;
	else return true;
}

void solve()
{
	sa(k);
	scanf("%s", val + 1);
	int len = strlen(val + 1);

	int en = 0;
	int res = 1;
	int flag = 0;
	for (int i = 1; i <= len; i++)
	{
		int flag = 0;
		while (true)
		{
			if (!check())
			{
				flag = 1;
				break;
			}
			if (en >= len)
			{
				flag = 2;
				break;
			}
			num[(en+1) & 1][val[en+1] - 'a']++;
			en++;
		}
		if (flag == 1)
			res = max(res, en - i);
		else
			res = max(res, en - i + 1);
		num[i&1][val[i] - 'a']--;
	}
	printf("%d\n", res);
}
```

### E.Root Change

> 有一个$N(N<10^5)$个节点的树，现在第$i$个询问是在第$i$点成为树的根的情况下，有多少个边删除掉之后不影响树的深度。

首先如果确定根节点，那么需要维护根到**最深的叶子结点的LCA**的那条链，记录这个树的深度与该链结点上的数。

现在要求每一个节点作为根，考虑转移，一个节点可以通过dfs知道其down的那条链，up值可以通过其父节点对子节点记录**后缀与前缀值**得到。

```C++
int n;
int vis[maxn];
int ans[maxn];
vector<int>edge[maxn];

struct Hei
{
	int h;
	int num;
	Hei() {};
	Hei(int x, int y)
	{
		h = x;
		num = y;
	}
	void merge(const Hei &b)
	{
		if (b.h>h)
		{
			h = b.h;
			num = b.num;
		}
		else if (b.h == h)
		{
			num = 0;
		}
	}
	Hei Go_up()
	{
		return Hei(h + 1, num + 1);
	}
};

Hei up[maxn], down[maxn];

void cal(int x)
{
	vis[x] = 1;
	down[x] = Hei(0, 0);
	for (int i = 0; i<edge[x].size(); i++)
	{
		int nxt = edge[x][i];
		if (vis[nxt])continue;
		cal(nxt);
		down[x].merge(down[nxt].Go_up());
	}
	vis[x] = 0;
}

void calDown(int x)
{
	Hei suf(0, 0);
	vis[x] = 1;
	for (int i = edge[x].size() - 1; i >= 0; i--)
	{
		int nxt = edge[x][i];
		if (vis[nxt])continue;
		up[nxt] = suf;
		suf.merge(down[nxt].Go_up());
	}
	Hei pre(0, 0);
	for (int i = 0; i<edge[x].size(); i++)
	{
		int nxt = edge[x][i];
		if (vis[nxt])continue;
		Hei h = up[x];
		h.merge(pre);
		h.merge(up[nxt]);
		up[nxt] = h.Go_up();
		pre.merge(down[nxt].Go_up());
		calDown(nxt);
	}
	pre.merge(up[x]);
	ans[x] = pre.num;
	vis[x] = 0;
}

void solve()
{
	memset(up, 0, sizeof(up));
	memset(down, 0, sizeof(down));
	sa(n);
	repp(i, 1, n - 1)
	{
		int x, y;
		sa(x), sa(y);
		edge[x].push_back(y);
		edge[y].push_back(x);
	}
	cal(1);
	calDown(1);
	repp(i, 1, n)
	{
		//DE(ans[i])
		printf("%d\n", n - 1 - ans[i]);
	}
}
```

