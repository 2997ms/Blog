---
title: Educational Codeforces Round 1
date: 2019-11-18 23:13:57
tags: [Codeforces, 计算几何]
categories: 题记
---

时隔多年，开始补的是Eductional Round，从第一轮开始，计算几何好难啊，我怎么这么菜啊。。。

Educational补完，我要做安德鲁系列！你看我什么时候可以把Edu补完。

<!--more-->

[题目链接]([https://codeforces.com/contest/598](https://codeforces.com/contest/598))

A, B, C,D好像都比较简单啊。这里主要想记录的是F题。

### E. Chocolate Bar

切蛋糕，算是非常经典的dp了。

```c++
#include <iostream>
#include <algorithm>
#include <cmath>
#include <vector>
#include <string>
#include <cstring>
#pragma warning(disable:4996)
using namespace std;
 
#define INF 1e9+7
 
int dp[32][32][52];
 
int main()
{
    int i, j, k, h, m;
    for (i = 0; i <= 30; i++)
    {
        for (j = 0; j <= 30; j++)
        {
            for (k = 0; k <= 50; k++)
            {
                if (k == i*j || k == 0)
                {
                    dp[i][j][k] = 0;
                }
                else
                {
                    dp[i][j][k] = INF;
                }
                for (h = 0; h <= k; h++)
                {
                    for (m = 1; m < j; m++)
                        dp[i][j][k] = min(dp[i][j][k], dp[i][m][h] + dp[i][j - m][k - h] + i*i);
                    for (m = 1; m < i; m++)
                        dp[i][j][k] = min(dp[i][j][k], dp[m][j][h] + dp[i - m][j][k - h] + j*j);
                }
            }
        }
    }
 
    scanf("%d", &k);
    while (k--)
    {
        scanf("%d%d%d", &i, &j, &h);
        printf("%d\n", dp[i][j][h]);
    }
    return 0;
}
```

### F. Cut length

题意是给出一个图形和一个直线，计算这个直线在该图形内的长度。

计算几何，用叉积算方向，从而算是否相交。

```c++

#include<cstdio>
#include<cmath>
#include<algorithm>
using namespace std;
const int N=1005;
const double eps=1e-5;
int n,m;
struct P
{
	double x,y;
	double length()
	{
		return sqrt(x*x+y*y);
	}
}a[N];
P operator-(const P &a,const P &b)
{
	return {a.x-b.x,a.y-b.y};
}
double cp(P a,P b)
{
	return a.x*b.y-b.x*a.y;
}
struct L
{
	double l;
	int v;
}stk[N];
int top;
bool cmp(L a,L b){return a.l<b.l; }
//这里用来计算比例
double Len(P a,P v,P p,P q)
{
	return cp(p-a,q-p)/cp(v,q-p);
}
int sign(double x)
{
	if (x<-eps) return -1;
	if (x>eps) return 1;
	return 0;
}
//Point, Vector
double solve(P p,P v)
{
	top=0;
	for (int i=1;i<=n;i++)
	{
		int s1=sign(cp(v,a[i-1]-p));
		int s2=sign(cp(v,a[i]-p));
		if (s1>0&&s2>0||s1==0&&s2==0||s1<0&&s2<0) continue;
		if (s1>s2) stk[++top]={Len(p,v,a[i-1],a[i]) , (s1!=0&&s2!=0 ?2:1) };
		else stk[++top]={Len(p,v,a[i-1],a[i]) , (s1!=0&&s2!=0 ?-2:-1) };
	}
	sort(stk+1,stk+top+1,cmp);
	double ret=0;
	int tmp=0;
	for (int i=1;i<=top;i++)
	{
		if (tmp) ret+=stk[i].l-stk[i-1].l;
		tmp+=stk[i].v;
	}
	return ret*v.length();
}
int main()
{
	scanf("%d%d",&n,&m);
	for (int i=1;i<=n;i++) scanf("%lf%lf",&a[i].x,&a[i].y);
	a[0]=a[n];
	for (int j=1;j<=m;j++)
	{
		P p,q;
		scanf("%lf%lf",&p.x,&p.y);
		scanf("%lf%lf",&q.x,&q.y);
		printf("%.10f\n",solve(p,q-p));
	}
}
```