---
title: CODE FESTIVAL 2017 qual B
date: 2017-11-04 22:35:28
tags: [AtCoder]
categories: [待填]
---

感觉还是应该多打一些contest，多在打的时候思考思考。。。

C题想了两个小时没思路啊，智障啊。。。

[题目链接](http://code-festival-2017-qualb.contest.atcoder.jp/)

<!--more-->

AB两个水题不说了。

### C - 3 Steps

> 题意是给出一个联通图，无自环和重边，然后不断的往里面添加边，添加边的条件是这两个点可以通过三条边连接，那么这两个点就可以添一条边。问最多添多少边。

居然。。。往二分图那方面去想了。。。

后来自己想想。。。有道理啊。。。

首先这个是一个联通图，那么如果不能变成二分图的话，就是有奇环了，那么画一下会发现，任意两个点最终都可以添一条边，答案就是$\frac{n*(n-1)}{2}-m$。

如果可以变成二分图的话呢。。。染色黑白，就是黑的数量*白的数量-m。。。

```C++
int n,m;
int col[maxn];
ll cnt1,cnt2;
vector<int>edge[maxn];

bool dfs(int x,int c)
{
	if(col[x]!=-1)
	{
		return col[x]==c;
	}
	col[x]=c;
	int res=1;
	for(int i=0;i<edge[x].size();i++)
	{
		int nxt=edge[x][i];
		res&=dfs(nxt,1-c);
	}
	return res;
}

void solve()
{
	sa(n),sa(m);
	
	repp(i,1,m)
	{
		int x,y;
		sa(x),sa(y);
		edge[x].push_back(y);
		edge[y].push_back(x);
	}
	memset(col,-1,sizeof(col));
	bool res = dfs(1,0);
	repp(i,1,n)
	{
		if(col[i])cnt1++;
		else cnt2++;
	}
	ll ans=-m;
	if(res)
	{
		ans+=cnt1*cnt2;
	}
	else
	{
		ans+=1LL*n*(n-1)/2;
	}
	cout<<ans<<endl;
}
```



### D 101 to 010

> 题意是给出一个字符串，要把101可以变成010，问最多操作次数。

最终的1来源只有三种情况：

1: 1

2: 111101

3: 101111

这样就可以转换成不重合区间的子串价值之和，使得区间的价值最大。可以达到$O(n^2)$。

可以把上面2、3情况预处理出来，dp可以优化到$O(n)$。

```C++
int n;
char v[maxn];
int val[maxn];
int c[maxn];
int dp[maxn];
vector<int>pos[maxn];
 
void solve()
{
    sa(n);
    scanf("%s",v+1);
    n = strlen(v+1);
    repp(i,1,n)
    {
        val[i] = v[i] - '0';
    }
 
    repp(i,1,n)
    {
        c[i]=val[i];
    }
    int st = -1;
    int flag = 0;
    repp(i,3,n)
    {
        if(c[i]&&flag)
        {
            pos[i].push_back(st);
        }
        else if(c[i] == 1&&c[i-1]==0&&c[i-2]==1)
        {
            flag = 1;
            st = i-2;
            pos[i].push_back(st);
        }
        else
        {
            flag = 0;
            st = -1;
        }
    }
    flag = 0;
    repp(i,1,n)
    {
        c[i] = val[i];
    }
    st = -1;
    for(int i=n-2;i>=1;i--)
    {
 
        if(c[i]&&flag)
        {
            pos[st].push_back(i);
        }
        else if(c[i] == 1&&c[i+1]==0&&c[i+2]==1)
        {
            flag = 1;
            st = i+2;
            pos[st].push_back(i);
        }
        else
        {
            flag = 0;
            st = -1;
        }
    }
    repp(i,3,n)
    {
        dp[i] = dp[i-1];
        rep(k,0,pos[i].size())
        {
            dp[i] = max(dp[i], dp[pos[i][k] - 1] + i - pos[i][k] - 1);
        }
    }
    cout<<dp[n]<<endl;
}
```



