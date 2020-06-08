---
title: Kickstart A Round 2017
date: 2017-03-11 16:14:00
tags: [Google]
categories: [题记]
---

这一轮的kickstart，第一题推了好久，才把large搞出来啊，后来才知道知乎上有讨论的了。。。这样下去要跪啊。。。

<!--more-->

### A.Square Counting

Problem

Mr. Panda has recently fallen in love with a new game called Square Off, in which players compete to find as many different squares as possible on an evenly spaced rectangular grid of dots. To find a square, a player must identify four dots that form the vertices of a square. Each side of the square must have the same length, of course, but it does not matter what that length is, and the square does not necessarily need to be aligned with the axes of the grid. The player earns one point for every different square found in this way. Two squares are different if and only if their sets of four dots are different.

Mr. Panda has just been given a grid with R rows and C columns of dots. How many different squares can he find in this grid? Since the number might be very large, please output the answer modulo 109 + 7 (1000000007).

Input

The first line of the input gives the number of test cases, T. T lines follow. Each line has two integers R and C: the number of dots in each row and column of the grid, respectively.
Output

For each test case, output one line containing Case #x: y, where x is the test case number (starting from 1) and y is the number of different squares can be found in the grid.
Limits

1 ≤ T ≤ 100.
Small dataset

2 ≤ R ≤ 1000.
2 ≤ C ≤ 1000.
Large dataset

2 ≤ R ≤ 10^9.
2 ≤ C ≤ 10^9.

Sample
Input 
4
2 4
3 4
4 4
1000 500

Output 
Case #1: 3
Case #2: 10
Case #3: 20
Case #4: 624937395

> 题意是n*m的点阵中有多少个正方形

算的时候各种推，很麻烦。。。包括
$$
1^2+2^2+3^2+....+n^2 = \frac{n(n+1)(2n+1)}{6}
$$

$$
1^3+2^3+3^3....+n^3=\frac{[n(n+1)]^2}{2}
$$

都用上了。。。

推一下：

$n\*n$的点阵所含有的正方形个数(包括正的和斜的)为$n-1$个

$n\*m$中含有$k\*k$的个数为$(n-k+1)(m-k+1)$个

所以能够得到
$$
ans=\sum\_{i=2}^{min(n,m)-1}(n-i+1)(m-i+1)(i-1) = \sum\_{i-1=1}^{min(n,m)-1}(n-(i-1))(m-(i-1))(i-1)
$$

$$
ans=\sum_{i=1}^{min(n,m)}(n-i)(m-i)i
$$

这样还不够。。。展开来

令$r=min(n,m)$,有
$$
ans=\sum\_{i=1}^{r}i^3-(n+m)\sum\_{i=1}^{r}i^2+nm\sum\_{i=1}^{r}i
$$
然后通过上面的求和公式展开，得到结果。。。这里直接看代码吧。。。

```c++
void solve()
{
	ll n,m;
	cin>>n>>m;
	if(n>m)swap(n,m);
	n--,m--;
	ll res1 = m*n%mod*(n+2)%mod*(n+1)%mod*po(2,mod-2,mod)%mod;
	ll res2 = (m+n)%mod*( (n+2)*(n+1)%mod*n%mod*po(3,mod-2,mod)%mod)%mod;
	ll res3 = n*(n+1)%mod*(2*n+1)%mod *po(6,mod-2,mod)%mod + n*n%mod*(n+1)%mod*(n+1)%mod*po(4,mod-2,mod)%mod;
	ll res=((res1-res2+res3)%mod+mod)%mod;
	cout<<res<<endl;
}
```

### B.Patterns Overlap

Problem

Alice likes reading and buys a lot of books. She stores her books in two boxes; each box is labeled with a pattern that matches the titles of all of the books stored in that box. A pattern consists of only uppercase/lowercase English alphabet letters and stars (\*). A star can match between zero and four letters. For example, books with the titles GoneGirl and GoneTomorrow can be put in a box with the pattern Gone\*\*, but books with the titles TheGoneGirl, and GoneWithTheWind cannot.

Alice is wondering whether there is any book that could be stored in either of the boxes. That is, she wonders if there is a title that matches both boxes' patterns.

Input

The first line of the input gives the number of test cases, T. T test cases follow. Each consists of two lines; each line has one string in which each character is either an uppercase/lowercase English letter or \*.

Output

For each test case, output one line containing Case #x: y, where x is the test case number (starting from 1) and y is TRUE if there is a string that matches both patterns, or FALSE if not.

Limits

1 ≤ T ≤ 50.
Small dataset

1 ≤ the length of each pattern ≤ 200.
Each pattern contains at most 5 stars.
Large dataset

1 ≤ the length of each pattern ≤ 2000.
Sample

Input 
3
\*\*\*\*
It
Shakes\*e
S\*speare
Shakes\*e
\*peare

Output 
Case #1: TRUE
Case #2: TRUE
Case #3: FALSE

> 题意是给出两个字符串，其中\*可以匹配0到4个字符，问有两种模式，有没有一个字符串满足这两种模式的。

很明显的dp了，但是期间一直犹豫碰到\*的时候要转移到下4个非\*的字符状态，就很麻烦，结束了才知道可以直接把\*替换成四个任意字符的。

调试了好久。。。。。。

```C++
int dp[maxn][maxn];
string change(string x)
{
	int len=x.length();
	string res;
	for(int i=0;i<len;i++)
	{
		if(x[i]=='*')
		{
			repp(k,1,4)
			{
				res.push_back(0);
			}
		}
		else
		{
			res.push_back(x[i]);
		}
	}
	return res;
}
void solve()
{
	string s,t;
	cin>>s>>t;
	s=change(s);
	t=change(t);
	memset(dp,0,sizeof(dp));
	dp[0][0]=1;
	for(int i=0;i<=s.length();i++)//注意这里的等号！！！！！！
	{
		for(int j=0;j<=t.length();j++)
		{
			if(!dp[i][j])continue;
			if(s[i]==t[j]||s[i]==0||t[j]==0)
			{
				dp[i+1][j+1]|=dp[i][j];
			}
			if(s[i]==0)
			{
				dp[i+1][j]|=dp[i][j];
			}
			if(t[j]==0)
			{
				dp[i][j+1]|=dp[i][j];
			}
		}
	}
	cout<<(dp[s.length()][t.length()]?"TRUE":"FALSE")<<endl;
}

```

### C. Space Cubes

Problem

"Look at the stars, look how they shine for you." - Coldplay, "Yellow"

In a galaxy far, far away, there are many stars. Each one is a sphere with a certain position (in three-dimensional space) and radius. It is possible for stars to overlap each other.

The stars are so incredibly beautiful to you that you want to capture them forever! You would like to build two cubes of the same integer edge length, and place them in space such that for each star, there is at least one cube that completely contains it. (It's not enough for a star to be completely contained by the union of the two cubes.) A star is completely contained by a cube if no point on the star is outside the cube; a point exactly on a cube face is still considered to be inside the cube.

The cubes can be placed anywhere in space, but they must be placed with their edges parallel to the coordinate axes. It is acceptable for the cubes to overlap stars or each other.

What is the minimum integer edge length that allows you to achieve this goal?

Input

The input starts with one line containing exactly one integer T, which is the number of test cases. T test cases follow.

Each test case begins with a line containing an integer, N, representing the number of stars.

This is followed by N lines. On the ith line, there are 4 space-separated integers, Xi, Yi, Zi and Ri, indicating the (X, Y, Z) coordinates of the center of the ith star, and the radius of the ith star.

Output

For each test case, output one line containing Case #x: y, where x is the test case number (starting from 1) and y is the minimum cube edge length that solves the problem, as described above.

Limits

1 ≤ T ≤ 100.
-108 ≤ Xi ≤ 108, for all i.
-108 ≤ Yi ≤ 108, for all i.
-108 ≤ Zi ≤ 108, for all i.
1 ≤ Ri ≤ 108, for all i.
Small dataset

1 ≤ N ≤ 16.
Large dataset

1 ≤ N ≤ 2000.

Sample

Input 
3
3
1 1 1 1
2 2 2 1
4 4 4 1
3
1 1 1 2
2 3 4 1
5 6 7 1
3
1 1 1 1
1 1 1 1
9 9 9 1
 	
Output 

Case #1: 3
Case #2: 5
Case #3: 2
In the first test case, one solution is to place two cubes with an edge length of 3 such that their corners with minimum (x, y, z) coordinates are at (0, 0, 0) and (3, 3, 3).
In the second test case, one solution is to place two cubes with an edge length of 5 such that their corners with minimum (x, y, z) coordinates are at (-1, -1, -1) and (1, 2, 3).

> 题意是在空间中给出了一些球体(坐标、半径)，现在你有两个正方体，要求把每一个球体包括进去，即每一个球体要么在A正方体中，要么在B正方体中，问最小的正方体边长。

如果是二维平面，那么我求出边界$x、y$之后(即$X\_{max} X\_{min} Y\_{max} Y\_{min}$)，那么直接二分正方形长度，肯定是左上、右下或者左下、右上这两种放置正方形的方式。三维的类似，8种方式放置。

```C++
int n;
int x[maxn],y[maxn],z[maxn],r[maxn];

bool inside(ll xd1,ll yd1,ll zd1,ll xu1,ll yu1,ll zu1,
			ll xd2,ll yd2,ll zd2,ll xu2,ll yu2,ll zu2)
{
	repp(i,1,n)
	{
		int ans=0;
		if(x[i]-r[i]<xd1 || x[i]+r[i]>xu1
			||y[i]-r[i]<yd1 || y[i]+r[i]>yu1
			||z[i]-r[i]<zd1 || z[i]+r[i]>zu1)
		{
			ans++;
		}
		if(x[i]-r[i]<xd2 || x[i]+r[i]>xu2
			||y[i]-r[i]<yd2 || y[i]+r[i]>yu2
			||z[i]-r[i]<zd2 || z[i]+r[i]>zu2)
		{
			ans++;
		}
		if(ans==2)return false;
	}
	return true;
}

bool check(ll len)
{
	ll xmin=INF,xmax=-INF;
	ll ymin=INF,ymax=-INF;
	ll zmin=INF,zmax=-INF;

	repp(i,1,n)
	{
		xmin=min(xmin,1LL*x[i]-r[i]);
		xmax=max(xmax,1LL*x[i]+r[i]);

		ymin=min(ymin,1LL*y[i]-r[i]);
		ymax=max(ymax,1LL*y[i]+r[i]);

		zmin=min(zmin,1LL*z[i]-r[i]);
		zmax=max(zmax,1LL*z[i]+r[i]);
	}

	int xd[4] = {xmin,xmin+len,xmax-len,xmax}; 
	int yd[4] = {ymin,ymin+len,ymax-len,ymax}; 
	int zd[4] = {zmin,zmin+len,zmax-len,zmax}; 

	for(int x1=0;x1<4;x1+=2)
	{
		for(int y1=0;y1<4;y1+=2)
		{
			for(int z1=0;z1<4;z1+=2)
			{
				for(int x2=0;x2<4;x2+=2)
				{
					for(int y2=0;y2<4;y2+=2)
					{
						for(int z2=0;z2<4;z2+=2)
						{
							if(inside(xd[x1],yd[y1],zd[z1],xd[x1+1],yd[y1+1],zd[z1+1],
									  xd[x2],yd[y2],zd[z2],xd[x2+1],yd[y2+1],zd[z2+1]))
							{
								return true;
							}
						}
					}
				}
			}
		}
	}
	return false;
}

void solve()
{
	sa(n);
	repp(i,1,n)
	{
		sa(x[i]),sa(y[i]),sa(z[i]),sa(r[i]);
	}
	ll le=0,ri=INF;
	while(le<ri)
	{
		ll mid = (ri+le)>>1;
		if(check(mid))
		{
			ri=mid;
		}
		else
		{
			le=mid+1;
		}
	}
	printf("%lld\n",le);
}
```



