---
title: CS academy Round 66
date: 2018-02-21 17:09:25
tags: [CS academy, 欧拉回路, 二分图匹配]
categories: [题记]
---

假期里的第二套。。。

<!--more-->

[题目链接](https://csacademy.com/contest/round-66/tasks/)，A、B、C都很水。

### D Flipping Matrix

> 题意是给出一个$N\*N$的01格子，可以交换任意两行，任意两列。问最终可否形成对角线都为1的矩形。并给出交换方案。

这里只需考虑交换任意两行即可。之前知乎上有一个问题就是判定是否存在$N$个$1$使得其在矩阵的不同行不同列。[问题链接](https://www.zhihu.com/question/53826972)

若val[i\][j\]=1，i到j建一条边。最终跑二分图匹配。记录最终匹配情况。

```C++
int n;
int vis[maxn];
bool mat[maxn][maxn];
int left_match[maxn],right_match[maxn];

int findmatch(int x)
{
    if(vis[x])
    {
        return false;
    }
    vis[x] = true;
    for(int i=1;i<=n;i++)
    {
        if(mat[x][i] && left_match[i] == 0)
        {
            right_match[x] = i;
            left_match[i] = x;
            return true;
        }
    }
    for(int i=1;i<=n;i++)
    {
        if(mat[x][i] && findmatch(left_match[i]))
        {
            right_match[x] = i;
            left_match[i] = x;
            return true;
        }
    }
    return false;
}
map<pair<int,int>,int>hax;
void solve()
{
    sa(n);
    repp(i,1,n)
    {
        repp(j,1,n)
        {
            sa(mat[i][j]);
        }
    }
    memset(left_match,0,sizeof(left_match));
    memset(right_match,0,sizeof(right_match));

    bool flag = true;
    do{
        flag = false;
        for(int i=1;i<=n;i++)
        {
            vis[i] = 0;
        }
        for(int i=1;i<=n;i++)
        {
            if(right_match[i]==0 && findmatch(i))
            {
                flag = true;
            }
        }
    }while(flag);
    int k = 0;
    repp(i,1,n)
    {
        k += right_match[i] != 0;
    }
    if(k != n)
    {
        puts("-1");
        return;
    }
    repp(i,1,n)
    {
        vis[i] = i;
    }
    repp(i,1,n)
    {
        if(right_match[i]==vis[i])continue;
        int j = 1;
        for(;j<=n;j++)
        {
            if(vis[j]==right_match[i])
            {
                break;
            }
        }
        cout<< "R "<<vis[j]<<" "<<vis[i]<<endl;
        int t = vis[j];
        vis[j] = vis[i];
        vis[i] = t;
    }
}
```

### E Domino Train

> 题意是给出了$N$个骨牌，即$N$对数。然后你要进行N-1步操作，每个操作处理两个骨牌的前后位置，最终使得相邻骨牌数字相同最多。

这个题目又是想了很久。。。其实就跑欧拉回路就好了。。。

每一个骨牌$(A_i,B_i)$，建立一个A_i到B_i的边，然后以每一个联通块为单位处理，对于有多个度为奇数的点，连一条边，然后从度为奇数的点作为起点，跑欧拉回路即可。。。

记录每一个点的最终位置，使用并查集得到处理的方案。

从答案出发感觉一切都不难，然而如何搞定真是难啊。。。

```C++
int n;
map<int, int>edge[maxn];

struct f
{
	int fa[maxn];
	int getfa(int x)
	{
		if (x == fa[x])return x;
		else
		{
			fa[x] = getfa(fa[x]);
			return fa[x];
		}
	}
}A, L, R;

int deg[maxn];
int flag[maxn], ans[maxn], pos[maxn];
vector<int>nums[maxn];
vector<int>path;

void dfs(int x)
{
	while (edge[x].size())
	{
		int id = edge[x].begin()->first;
		int to = edge[x].begin()->second;
		edge[x].erase(id);
		edge[to].erase(-id);
		dfs(to);
		path.push_back(id);
	}
}

void solve()
{
	sa(n);
	repp(i, 1, n)
	{
		A.fa[i] = i;
	}
	repp(i, 1, n)
	{
		int x, y;
		sa(x), sa(y);
		edge[x][i] = y;
		edge[y][-i] = x;
		deg[x] ++;
		deg[y] ++;
		A.fa[A.getfa(x)] = A.getfa(y);
	}
	repp(i, 1, n)
	{
		nums[A.getfa(i)].push_back(i);
	}
	int cnt = 0;
	int m = n;
	repp(i, 1, n)
	{
		if (nums[i].size() == 0)continue;
		vector<int>odd;
		rep(k, 0, nums[i].size()) {
			if (deg[nums[i][k]] % 2) {
				odd.push_back(nums[i][k]);
			}
		}
		while (odd.size()>2)
		{
			int k1 = odd.back(); odd.pop_back();
			int k2 = odd.back(); odd.pop_back();
			m++;
			edge[k1][m] = k2;
			edge[k2][-m] = k1;
		}
		int start = i;
		if (odd.size())start = odd[0];
		dfs(start);
		while (path.size() != 0)
		{
			int id = path.back();
			path.pop_back();
			if (abs(id) <= n)
			{
				if (id<0)
				{
					flag[abs(id)] = 1;
				}
				ans[++cnt] = abs(id);
			}
		}
	}
	repp(i, 1, n)
	{
		L.fa[i] = i;
		R.fa[i] = i;
		pos[ans[i]] = i;
	}
	repp(i, 1, n - 1)
	{
		int x;
		sa(x);
		x = pos[x];
		x = L.getfa(x);
		if (x == 1)
		{
			int y = R.getfa(x) + 1;
			printf("%d %d\n", ans[x], ans[y]);
			R.fa[R.getfa(x)] = y;
			L.fa[L.getfa(y)] = x;
		}
		else
		{
			int y = L.getfa(x - 1);
			printf("%d %d\n", ans[y], ans[x]);
			R.fa[R.getfa(y)] = x;
			L.fa[L.getfa(x)] = y;
		}
	}
	repp(i, 1, n)
	{
		if (i>1)printf(" ");
		printf("%d", flag[i]);
	}
}

```



