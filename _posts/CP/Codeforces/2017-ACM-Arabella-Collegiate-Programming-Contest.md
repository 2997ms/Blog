---
title: 2017 ACM Arabella Collegiate Programming Contest
date: 2017-05-01 20:41:26
tags: [gym]
categories: [题记]
---

挂在CF GYM的一套题，据说只有DIV2的难度，就做了一下，前面还好，后面做了好久，才清理干净。

暑期去实习，还有两个月，这两个月要做的事情有很多很多，多多看书，多多运动。

<!--more-->

[题目链接](http://codeforces.com/gym/101350)

### A. Sherlock Bones

> 给出一个由01组成的字符串，问有多少个三元组$i,j,k$，满足区间$i$到$j$中$1$的数量等于区间$j$到$k$中1的数量。

算每两个连续的$1$中$0$的数量，然后找规律。。。分段计算每一段产生的贡献。

```C++
char x[maxn];
int  last_le[maxn], suf[maxn];
vector<int>val;
void solve()
{
	val.clear();
	memset(suf, 0, sizeof(suf));

	int len;
	sa(len);
	scanf("%s", x + 1);

	int last = 0;
	repp(i, 1, len)
	{
		last_le[i] = last;
		if (x[i] == '1')
		{
			last = 0;
		}
		else
		{
			last++;
		}
	}
	repp(i, 1, len)
	{
		if (x[i] == '1')
		{
			val.push_back(last_le[i]);
		}
	}
	if (x[len] == '0')
		val.push_back(last_le[len] + 1);
	else
		val.push_back(0);
	ll ans = 0;
	for (int i = 1; i < val.size(); i++)
	{
		ans += 1LL * val[i] * val[i - 1];
	}
	for (int i = val.size() - 1; i >= 0; i--)
	{
		suf[i] = suf[i + 2] + val[i] + 1;
	}
	for (int i = 0; i < val.size(); i++)
	{
		ans += 1LL*(val[i] + 1)*suf[i + 3];
	}
	printf("%lld\n", ans);
}
```

### B. Unusual Team

> 水题，判断大小。

```C++
int a,b;
void solve()
{
	sa(a),sa(b);
	if(a<b)
	{
		puts("WeWillEatYou");
	}
	else
	{
		puts("FunkyMonkeys");
	}
}
```

### C. Cheap Kangaroo

> 一共$n$个人，每个人要吃$x\_i$的食物，所用的盘子大小与食物的数量相等，花费与盘子的大小相等。每个人希望用相同大小的盘子，问最小花费和可以使用的最大盘子。

说得很绕，又是一个水题。最小花费求和，最大盘子所有数的gcd。

```C++
int n;
int val[maxn];

void solve()
{
	sa(n);
	ll sum=0;
	repp(i,1,n)
	{
		sa(val[i]);
		sum+=val[i];
	}
	int res=val[1];
	repp(i,1,n)
	{
		res=gcd(res,val[i]);
	}
	printf("%lld %d\n",sum,res);
}
```

### D. Magical Bamboos

> $n$个竹子，每个竹子高度$h\_i$，现在你可以对其中一个竹子的高度减$1$，其余竹子高度均加$1$，问能否最终所有竹子的高度相等。

挺有意思的题目，那天晚上把这个题想复杂了。首先多个相同的竹子就可以当成一个竹子了，然后考虑从最高的竹子减1，其余竹子加1，为了最高与次高的竹子相等，需要他们同奇同偶。之后这两个竹子可以当成一个竹子了，往下递推下去就好了。

```C++
int n;
int val[maxn];

void solve()
{
	sa(n);
	repp(i,1,n)
	{
		sa(val[i]);
	}
	sort(val+1,val+n+1);
	repp(i,2,n)
	{
		if((val[i]+val[i-1])&1)
		{
			puts("no");
			return;
		}
	}
	puts("yes");
}
```

### E. Competitive Seagulls

> 首先有$n$个全部为白色的点，排成一排。两个人进行如下的游戏：假设$L$是最长连续为白色点的长度，那么可以挑选一个长度$P$，要求$P$是质数且小于等于$L/2$，将这些点染成黑色。不断重复上述步骤，不能进行游戏的人算输。现给定长度$n$，问双方都最优，输赢情况。

当$n$大于等于$4$的情况下 ，先手可以在中间染$2$个或者$3$个点，使得两边是相同的情况，这样后手必败。



### F. Monkeying Around

> 题意是有N个猴子在座位上讲笑话，每个猴子讲的笑话会有种类和范围，当一个猴子听到没有听过的笑话的时候，它会从座位上离开，掉地上；当听到已经听过的笑话的时候，它会从地上回到座位。现在给出各个讲笑话的情况，问最终猴子有多少在座位上。

把这个转换成区间问题，按照所有区间的左端点排序，右端点排序。枚举每一个猴子，维护当前影响该点的笑话，如果最后的笑话出现$2$次以上，那么该猴子在座位上。

```C++
int n, m;
struct no
{
	int id;
	int le;
	int ri;
}node1[maxn], node2[maxn];

int val[maxn];
bool cmp1(const no&a, const no &b)
{
	return a.le<b.le;
}
bool cmp2(const no&a, const no &b)
{
	return a.ri<b.ri;
}
int num[maxn];
set<int>s;

void solve()
{
	s.clear();
	memset(num, 0, sizeof(num));

	sa(n), sa(m);
	repp(i, 1, m)
	{
		int x, in, k;
		sa(x), sa(in), sa(k);
		val[i] = in;
		node1[i].le = x - k;
		node1[i].ri = x + k;
		node1[i].id = i;

		node2[i].le = x - k;
		node2[i].ri = x + k;
		node2[i].id = i;
	}
	sort(node1 + 1, node1 + m + 1, cmp1);
	sort(node2 + 1, node2 + m + 1, cmp2);

	int c1 = 1, c2 = 1;
	int ans = 0;
	repp(i, 1, n)
	{
		while (c1 <= m&&node1[c1].le <= i)
		{
			num[val[node1[c1].id]]++;
			s.insert(node1[c1].id);
			c1++;
		}
		while (c2<=m&&node2[c2].ri<i)
		{
			num[val[node2[c2].id]]--;
			s.erase(node2[c2].id);
			c2++;
		}
		if (s.empty())ans++;
		else
		{
			int k = *s.rbegin();
			if (num[val[k]] > 1)ans++;
		}
	}
	printf("%d\n", ans);
}
```



### G. Snake Rana

> hihocoder上面的[一道题](http://hihocoder.com/contest/offers8/problem/4)

$K<=20$，容斥原理。

首先算出所有格子的数量，格子为$r\*c$的数量有$(N-r+1)\*(M-c+1)$个，枚举$r$和$c$。
$$
\sum\_{r=1}^{N}\sum\_{c=1}^{M}(N-r+1)(M-c+1) = \frac{(N+1)\*N}{2}\*\frac{(M+1)\*M}{2}
$$
然后容斥减去有一个黑格子的，加上有两个格子的。。。。

至于算有$g$个黑格子有多少个，分别计算$x,y$的最大值$maxx,maxy$，最小值$minx,miny$。结果等于$minx\*miny\*(N-maxx+1)\*(N-maxy+1)$。

```C++
int n,m,k;
int vx[25],vy[25];

void solve()
{
	sa(n),sa(m),sa(k);
	rep(i,0,k)
	{
		sa(vx[i]),sa(vy[i]);
	}
	ll ans=1LL*n*(n+1)/2*m*(m+1)/2;
	for(int i=1;i<(1<<k);i++)
	{
		int g=i;
		int cnt=0;
		while(g)
		{
			cnt+=(g&1);
			g>>=1;
		}
		int minx=1e9,maxx=-1,miny=1e9,maxy=-1;
		rep(h,0,k)
		{
			if((i>>h)&1)
			{
				maxx=max(maxx,vx[h]);
				maxy=max(maxy,vy[h]);
				minx=min(minx,vx[h]);
				miny=min(miny,vy[h]);
			}
		}
		ll res=1LL*minx*miny*(n-maxx+1)*(m-maxy+1);
		if(cnt&1)ans-=res;
		else ans+=res;
	}
	printf("%lld\n", ans);
}
```



### H. Mirrored String I

> 水题，按照题意搞一遍就好了。。。

```C++
int a,b;
bool check(char x)
{
	return x=='A'||x=='H'||x=='I'||x=='M'||x=='O'||x=='T'||x=='U'||x=='V'||x=='W'||x=='X'||x=='Y';
}
void solve()
{
	string x;
	cin>>x;
	int le=0,ri=x.length()-1;
	while(le<=ri)
	{
		if(x[le]!=x[ri])
		{
			puts("no");
			return;
		}
		if(!check(x[le]))
		{
			puts("no");
			return;
		}
		le++,ri--;
	}
	puts("yes");
}
```



### I. Mirrored String II

>问有多少回文子串。

长度在$1000$范围内，dp。

```C++
char val[maxn];
int dp[maxn][maxn];

bool check(char x)
{
	return x == 'A' || x == 'H' || x == 'I' || x == 'M' || x == 'O' || x == 'T' || x == 'U' || x == 'V' || x == 'W' || x == 'X' || x == 'Y';
}

void solve()
{
	memset(dp, -1, sizeof(dp));
	scanf("%s", val + 1);
	int n = strlen(val + 1);
	//cout << n << endl;
	repp(i, 1, n)
	{
		if (check(val[i]))dp[i][i] = 1;
	}
	repp(i, 2, n)
	{
		if ((!check(val[i])) || (!check(val[i])))
		{
			continue;
		}
		if (val[i] == val[i - 1])dp[i - 1][i] = 1;
	}
	
	for (int len = 3; len <= n; len++)
	{
		for (int st = 1; st <= n; st++)
		{
			int en = st + len - 1;
			if (en > n)continue;
			if ((!check(val[st])) || (!check(val[en])))
			{
				dp[st][en] = -1;
				continue;
			}
			if (val[st] == val[en] && dp[st + 1][en - 1] != -1)
			{
				dp[st][en] = 1;
			}
		}
	}
	int maxx = 0;
	for (int i = 1; i <= n; i++)
	{
		for (int j = i; j <= n; j++)
		{
			if (dp[i][j] == 1)
			{
				maxx = max(maxx, j - i + 1);
			}
		}
	}
	printf("%d\n", maxx);
}
```



### J. Lazy Physics Cat

>一个圆，里面有一个三角形，三个顶点分别在圆心、圆上、圆上。问扇形区域剪掉三角形面积那部分面积是多少。。。

```C++
double len,a;
void solve()
{
	scanf("%lf%lf",&len,&a);
	double k =a;
	//cout<<a<<endl;
	a=a/180.0*PI;
	double all = PI*len*len*k/360;
	//printf("%lf\n",a);
	//printf("%lf\n",0.5*len*len*sin(a));
	double res=all-0.5*len*len*sin(a);
	printf("%lf\n",res);
}
```



### K. Owl Geeks

写到这里发现水题也太多了。。。这个题按照题意模拟一下就好了。。。

```C++
int a,b,n;
vector<int>val;
int vis[maxn];
int cnt[15];

void solve()
{
	val.clear();
	memset(vis,0,sizeof(vis));
	sa(a),sa(b),sa(n);
	repp(x,1,n)
	{
		ll v=1LL*a*x*x+1LL*b*x;
		if(v>n||vis[v])continue;
		vis[v]=1;
		val.push_back(v);
	}
	if(val.size()==0)
	{
		puts("-1");
		return;
	}
	memset(cnt,0,sizeof(cnt));
	for(int i=0;i<val.size();i++)
	{
		int k=val[i];
		int c[15];
		memset(c,0,sizeof(c));
		while(k)
		{
			c[k%10]++;
			k/=10;
		}
		int maxx=0;
		repp(j,0,9)
		{
			maxx=max(maxx,c[j]);
		}
		repp(j,0,9)
		{
			if(c[j]==maxx)cnt[j]++;
		}
	}
	int maxx=0;
	repp(i,0,9)
	{
		maxx=max(maxx,cnt[i]);
	}
	repp(i,0,9)
	{
		if(cnt[i]==maxx)
		{
			printf("%d\n",i);
			return;
		}
	}
}

```



### L. All’s Wall That Ends Wall

> Leetcode上面的一个题，给出各个位置的高度，然后问在一维的情况下能够围出的水的量。二维的题也有(优先队列)。现在有$Q$次询问，每次可以对位置$X$的高度增加$V$，然后动态查询当时可以围出的水的量。

想了很久。。。不会做。。。看了其他人的做法。。。

从左到右维护一个递增的序列，通过这个序列来计算水的量。同理，从右到左也维护这么一个递增的序列。

每次更改，去找前后的位置即可。。。

感觉这个题复杂度有些迷啊。。。

```C++
int n,q;
ll ans;
ll sum[maxn],h[maxn];
set< pair<ll,ll> >leval,rival;
set< pair<ll,ll> >::iterator it;

int lowbit(int x)
{
	return x&(-x);
}

void add(int x,int val)
{
	while(x<=n)
	{
		sum[x]+=val;
		x+=lowbit(x);
	}
}

ll getsum(int x)
{
	ll res=0;
	while(x)
	{
		res+=sum[x];
		x-=lowbit(x);
	}
	return res;
}

ll cal(ll le,ll ri,ll pre)
{
	if(le==0||ri==n+1)return 0;
	ll t=getsum(ri)-getsum(le-1);
	ll res = 1LL*pre*(ri-le+1)-t;
	return res;
}

void upA(int x,int v)
{
	it = leval.lower_bound(mp(x,h[x]));
	if(it==leval.end())
	{
		--it;
		if(h[x]+v>it->second)
		{
			ans+=cal(it->first,x-1,it->second);
			leval.insert(mp(x,h[x]+v));
		}
		return;
	}
	int ph,px;
	if(leval.count(mp(x,h[x])))
	{
		ph=h[x];
		px=x;
		leval.erase(mp(x,h[x]));
	}
	else
	{
		--it;
		if(h[x]+v<=it->second)
		{
			ans-=v;
			return;
		}
		px=x;
		ph=it->second;
	}
	while(true)
	{
		it = leval.lower_bound(mp(x,0));
		if(it==leval.end())break;
		ans-=cal(px,it->first-1,ph);

		if(h[x]+v<it->second)
		{
			ans+=cal(x,it->first-1,h[x]+v);
			ans-=v;
			break;
		}
		px=it->first;
		ph=it->second;
		leval.erase(it);
	}
	leval.insert(mp(x,h[x]+v));
}

void upB(int x,int v)
{
	it = rival.lower_bound(mp(-x,h[x]));
	if(it==rival.end())
	{
		it--;
		if(h[x]+v>=it->second)
		{
			ans+=cal(x+1,-it->first,it->second);
			rival.insert(mp(-x,h[x]+v));
		}
		return;
	}
	int ph,px;
	if(rival.count(mp(-x,h[x])))
	{
		ph=h[x];
		px=x;
		rival.erase(mp(-x,h[x]));
	}
	else
	{
		--it;
		if(h[x]+v<it->second)
		{
			ans-=v;
			return;
		}
		px=x;
		ph=it->second;
	}
	while(true)
	{
		it = rival.lower_bound(mp(-x,0));
		if(it==rival.end())break;
		ans-=cal(-it->first+1,px,ph);

		if(h[x]+v<=it->second)
		{
			ans+=cal(-it->first+1,x,h[x]+v);
			ans-=v;
			break;
		}
		px=-it->first;
		ph=it->second;
		rival.erase(it);
	}
	rival.insert(mp(-x,h[x]+v));
}

void solve()
{
	ans=0;
	leval.clear();
	rival.clear();

	sa(n),sa(q);
	repp(i,1,n)sum[i]=0;
	repp(i,1,n)
	{
		scanf("%lld",&h[i]);
		add(i,h[i]);
	}
	int px=0,ph=0;
	repp(i,1,n)
	{
		if(h[i]>ph)
		{
			ans+=cal(px,i-1,ph);
			px = i;
			ph = h[i];
			leval.insert(mp(i,h[i]));
		}
	}
	px=n+1,ph=0;
	for(int i=n;i>=1;i--)
	{
		if(h[i]>=ph)
		{
			ans += cal(i+1,px,ph);
			px = i;
			ph = h[i];
			rival.insert(mp(-i,h[i]));
		}
	}
	char op[5];
	repp(i,1,q)
	{
		scanf("%s",op);
		if(op[0]=='P')
		{
			printf("%lld\n",ans);
		}
		else
		{
			int x,v;
			sa(x),sa(v);
			upA(x,v);
			upB(x,v);
			h[x]+=v;
			add(x,v);
		}
	}
}
```



### M. Make Cents?

> 给出各种钱币和汇率，问最终有多少钱。

```C++
int c,n;
char val[15];
map<string,double>hax;
void solve()
{
	hax.clear();
	sa(c),sa(n);
	repp(i,1,c)
	{
		double num;
		scanf("%s%lf",val,&num);
		string s=val;
		hax[s]=num;
	}
	hax["JD"]=1;
	double sum=0;
	repp(i,1,n)
	{
		double num;
		scanf("%lf%s",&num,val);
		string s=val;
		sum+=1.0*num*hax[s];
	}
	printf("%.6lf\n",sum);
}
```

