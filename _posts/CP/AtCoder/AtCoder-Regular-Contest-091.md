---
title: AtCoder Regular Contest 091
date: 2018-03-21 22:15:43
tags: [AtCoder, 博弈]
categories: [题记]
---

最近这个三月在忙毕业的事情，答辩、各种表格、医院、签证等等。在结束这一切之后，这一期的AtCoder终于补完了。艰难脸^ - ^

C、D不用说什么的话，那一共才两道题。。。

Update: [2018-06-17]就这么一直拖到了现在。。。

<!--more-->

[题目链接](https://arc091.contest.atcoder.jp/assignments)

### C - Flip,Flip, and Flip......

> 题意是给出了$n*m$个格子，然后每个格子都要进行翻转，连带着周围八个格子也都要翻转。问最终有多少格子是反面。

```C++
ll n, m;
void solve()
{
	cin >> n >> m;
	ll ans = n*m;
	int f = 0;
	if (m >= 2)
	{
		f++;
		ans -= 2 * n;
	}
	if (n >= 2)
	{
		f++;
		ans -= 2 * m;
	}
	if(f==2)ans += 4;
	cout << ans << endl;
}
```

### D Remainder Reminder

>找出不超过$n$中，有多少对$(a,b)$，使得$a\%b >= k$

枚举$b$。

```C++
ll n, k;
void solve()
{
	slla(n), slla(k);
	ll ans = 0;
	repp(x, k + 1, n)
	{
		ll f = n / x;
		ll num = x - k;
		ans += num*f;
		ll sur = n%x + 1;
		ans += max(sur - k, 0LL);
	}
	if (!k)ans = n*n;
	cout << ans << endl;
}
```

### E LISDL

> 给出$n，a，b$。构建一个长度为$n$的序列，元素是$1$到$n$。使得最长递增子序列长度为a，最长递减子序列长度为$b$。

这个题比较有趣了。。。可以先满足最长递增子序列为$a$，然后成段去构造最长递减子序列，这时也就可以发现满足条件是什么了。

```c++
ll n,a,b;
void solve()
{
    slla(n),slla(a),slla(b);
    if(a+b-1>n || a*b<n)
    {
        cout<<-1<<endl;
        return;
    }
    while(n>0)
    {
        int x = min(a,n-b+1);
 
        for(int i = n-x+1;i<=n;i++)
        {
            cout<<i<<" ";
        }
        n-=x;
        b--;
    }
}
```

### F - Strange Nim

> 有$n$堆石子，每堆石子的数量为$A_i$，每堆石子都有一个参数$k_i$。现有a和b，分别进行如下操作：
>
> 选择一堆石子，如果当前该堆有$x$个石子，那么移除$[1, floor(x/k)]$。
>
> 不能进行操作的人判输。双方都是最优选择，问输赢情况。

sg找规律。。。

如果$n$整除$k$，那么sg(n,k)=n/k

发现g(1,k), g(2,k), g(3,k)…. g(n,k)中移除g(k,k) g(2\*k,k), g(3\*k,k)...数列没有变化。

如果$n$不整除$k$，那么假设n=k\*x+b，有sg(x\*k+b,k) = sg((x-1)*k+(b-1),k).

可以发现原来是x\*k+b，现在要变成x\*k+b-(x+1)。这里需要进行优化，计算有多少个x是不变的。一共要减ceil(b/(x+1))次，所以也就是(b+x)/(x+1)。

```C++
int SG(int a,int k)
{
    int x=a/k,b=a%k;
    if(!b) return x;
    int t=(b+x)/(x+1);
    return SG(a-t*(x+1),k);
}
```

```c++
int n;
 
int cal(int x,int y)
{
    if(x%y==0)
    {
        return x/y;
    } else{
        return cal(x - max(x%y/(x/y+1),1)*(x/y+1),y);
    }
}
 
void solve()
{
    sa(n);
    int ans = 0;
    repp(i,1,n)
    {
        int x,y;
        sa(x),sa(y);
        ans ^= cal(x,y);
    }
    if(ans)
    {
        puts("Takahashi");
    }
    else
    {
        puts("Aoki");
    }
}
```

