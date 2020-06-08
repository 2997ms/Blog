---
title: Codeforces 452 Div2
date: 2017-12-21 10:47:55
tags: [Codeforces]
categories: [题记]
---



真是忘了初心，越写越差。。。

写个总结吧，怎么能这么菜啊。。。

<!--more-->

B题差了一个！号判断错了。。。

```C++

vector<int>ans;
bool check(int x)
{
	if(x%400==0)return true;
	if((x%4==0)&&(x%100!=0))
		return false;
	if(x%4==0)
		return true;
	return false;
}


void solve()
{
	repp(i,1,5000)
	{
		if(!check(i))
		{
			ans.push_back(31);
			ans.push_back(28);
			ans.push_back(31);
			ans.push_back(30);
			ans.push_back(31);

			ans.push_back(30);
			ans.push_back(31);
			ans.push_back(31);
			ans.push_back(30);
			ans.push_back(31);
			ans.push_back(30);
			ans.push_back(31);
		}
		else
		{
			ans.push_back(31);
			ans.push_back(29);
			ans.push_back(31);
			ans.push_back(30);
			ans.push_back(31);

			ans.push_back(30);
			ans.push_back(31);
			ans.push_back(31);
			ans.push_back(30);
			ans.push_back(31);
			ans.push_back(30);
			ans.push_back(31);
		}
	}

	int n;
	sa(n);
	vector<int>v;
	rep(i,0,n)
	{
		int x;
		sa(x);
		v.push_back(x);
	}
	rep(i,0,ans.size())
	{
		if(ans[i]==v[0])
		{
			int flag = 1;
			for(int k=0;k<v.size();k++)
			{
				if(i+k>=ans.size())
				{
					flag=0;
					break;
				}
				if(ans[i+k]!=v[k])
				{
					flag=0;
					break;
				}
			}
			if(flag)
			{
				puts("YES");
				return;
			}
		}
	}
	puts("NO");
	return;
}
```

大部分时间卡在D题上了，写出来的时候已经还剩半个小时了。~~一会看一下D题自己思路哪里不好了~~

我的方法是找规律。。。首先找到5，50，500，5000。。。这样的节点，然后发现在已添加的数里面增加的对数是1+2+3这样子的，特殊的就是已9结尾的数字，再把这个减掉就好了。感觉还是麻烦了啊？

```C++
ll v5[20];

ll cal(ll x,ll y,ll m)//<=x y的数量
{
	ll h = x/m;
	h += (x%m==y?1:0);
	return h;
}

void solve()
{
	ll x = 5;
	repp(i,1,10)
	{
		v5[i]=x;
		x*=10;
	}
	ll k;
	slla(k);
	if(k==1)
	{
		puts("0");
		return;
	}
	if(k==2)
	{
		puts("1");
		return;
	}
	if(k==3)
	{
		puts("3");
		return;
	}
	if(k==4)
	{
		puts("6");
		return;
	}
	int pos = upper_bound(v5+1,v5+10+1,k)-v5;
	pos--;
	ll d = 10;
	rep(i,1,pos)
	{
		d*=10;
	}
	ll cnt = (k-v5[pos]+1)/d;
	ll final = v5[pos]+cnt*d-1;
	ll ans = (1+cnt)*cnt/2*d;
	ans+=(k-final)*(cnt+1);
	ll p = 9;
	ll m = 10;
	rep(i,1,pos)
	{
		p=p*10+9;
		m=m*10;
	}
	ans-=cal(k,p,m);
	cout<<ans<<endl;
}

```

E题自己之前做过51nod上的[最大M子段和V2](http://www.51nod.com/onlineJudge/questionCode.html#!problemId=1053)，然而E题到最后还是没调出来。。。。

```C++
ll n, m;
ll v[maxn], val[maxn], le[maxn], ri[maxn];
ll pos_val[maxn];
set< pair<ll, int> >heap;

void del(int x)
{
	int Le = le[x], Ri = ri[x];
	if (Le)
		ri[Le] = Ri;
	if (Ri)
		le[Ri] = Le;
}

void solve()
{
	slla(n);
	repp(i, 1, n)
	{
		slla(v[i]);
	}
	v[0] = -1;

	int cnt = 1;
	int len = 1;

	repp(i, 2, n)
	{
		if (v[i] != v[i - 1])
		{
			val[cnt] = len;
			pos_val[cnt] = v[i - 1];
			heap.insert(make_pair(-len, cnt));
			cnt++;
			len = 1;
		}
		else
		{
			len++;
		}
	}
	pos_val[cnt] = v[n];
	val[cnt] = len;
	heap.insert(make_pair(-len, cnt));
	for (int i = 1; i <= cnt; i++)
	{
		le[i] = i - 1, ri[i] = (i == cnt ? 0 : i + 1);
	}
	int ans = 0;
	int k, j;
	while (true)
	{
		ans++;
		if (heap.size() == 1)
		{
			break;
		}
		k = heap.begin()->second, j = heap.begin()->first, heap.erase(heap.begin());
		if (pos_val[le[k]] == pos_val[ri[k]])
		{
			int sum = (val[le[k]] + val[ri[k]]);
			heap.erase(make_pair(-(val[le[k]]), le[k]));
			heap.erase(make_pair(-(val[ri[k]]), ri[k]));
			int t = le[k];
			val[ri[k]] = 0;
			del(ri[k]);
			del(k);
			val[k] = 0;
			val[t] = sum;
			heap.insert(make_pair(-(sum), t));
		}
		else
		{
			val[k] = 0;
			del(k);
		}
	}
	cout << ans << endl;
}
```

F题主要是不知道字符串怎么向前移动啊，思路卡在这里了啊。。。

啊，原来。。。树状数组就可以做这种事啊。。。为什么我。。。到现在还不知道啊。。。

哦。。。原来就是二分就可以了，多一个log。。。就是给定一个前缀和K，要求的就是前缀和为K的位置。二分查找就做到两个log了。谷歌面试的时候问到过一个log的想法。

```C++
int n, m;
int val[300];
set<int>pos[300];
char v[maxn];
ll num[maxn];
int lowbit(int x)
{
	return x&(-x);
}
ll getsum(ll x)
{
	ll ans = 0;
	while (x>0)
	{
		ans += num[x];
		x -= lowbit(x);
	}
	return ans;
}
ll add(int x, int v)
{
	int up = maxn - 1;
	while (x <= up)
	{
		num[x] += v;
		x += lowbit(x);
	}
	return x;
}

int find_p(int k)
{
	int le = 1, ri = n;
	while (le < ri)
	{
		int mid = (le + ri) / 2;
		if (getsum(mid) < k)
		{
			le = mid + 1;
		}
		else
		{
			ri = mid;
		}
	}
	return le;
}

void solve()
{
	sa(n), sa(m);
	scanf("%s", v + 1);
	repp(i, 1, n)
	{
		pos[v[i]].insert(i);
		add(i, 1);
	}
	repp(i, 1, m)
	{
		int le, ri;
		char op[5];
		scanf("%d%d%s", &le, &ri, op);
		char k = op[0];
		le = find_p(le);
		ri = find_p(ri);

		auto p = pos[k].lower_bound(le);
		while (p != pos[k].end()&&*p <= ri)
		{
			add(*p, -1);
			auto t = p;
			p++;
			pos[k].erase(t);
		}
	}
	repp(i, 1, n)
	{
		if (getsum(i) - getsum(i - 1))
		{
			printf("%c", v[i]);
		}
	}
}

```



