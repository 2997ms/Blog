---
title: Technocup 2017 - Elimination Round 1
date: 2016-10-19 18:03:25
tags: [Codeforces, AC自动机, DP]
categories: [题记]
---

最近去参加了复旦大学举办的Hackx_FDU，收获满满~ 看到了很多大神，然后也赞叹于一群人在短时间内可以写出如此美妙的代码。

这个Technocup是我Hackx比赛的时候没有吃晚饭，偷着两个小时打的。。。然而效果非常不好。。。自己脑袋犯困，另外也挺着急的。。。B题挂在精度上了。。。C题也脑残了好久啊。。。才出掉。。。

[题目链接](http://codeforces.com/contest/727)与[代码链接](https://github.com/2997ms/My_Algorithm/tree/master/Codeforces/Technocup%202017%20%E2%80%94%20Elimination%20Round%201)



### A  Transformation: from A to B

> 一个数字可以乘$2$，也可以乘$10+1$，问从$A$变到$B$的方案。

宽搜。

### B Bill Total Value

> 题意是给出一个字符串，里面是各个物品的价钱，如果小数点后面是两位的话，代表是小数。三位的话就是整数。问价钱总和。

莫名其妙地卡精度。。。本地运行OK，提交上去一直WA。下把这种情况直接乘以100，换成整数运算。。。

```c++
char x[maxn];

void solve()
{
	scanf("%s", x + 1);
	ll res = 0;
	for (int i = 1; x[i]; i++)
	{
		if (isalpha(x[i]))continue;
		ll t = 0;
		int dot = -1;
		while (x[i]&&!isalpha(x[i]))
		{
			if (x[i] == '.')
			{
				dot = i;
			}
			else
			{
				t = t * 10 + x[i] - '0';
			}
			i++;
		}
		if (dot == -1 || i - dot == 4)
		{
			t *= 100;
		}
		res += t;
		i--;
	}
	if (res < 100)
	{
		printf("0.%02lld\n", res);
		return;
	}
	vector<int>r;
	while (res)
	{
		r.push_back(res % 10);
		res /= 10;
	}
	reverse(r.begin(), r.end());

	for (int i = 0; i < r.size()-2; i++)
	{
		printf("%d", r[i]);
		if ((r.size() - 2 - (i+1)) % 3 == 0 && i != r.size() - 3)
		{
			printf(".");
		}
	}
	if (r[r.size() - 1] == 0 && r[r.size() - 2] == 0)
	{
		return;
	}
	else
	{
		printf(".%d%d\n", r[r.size() - 2], r[r.size() - 1]);
	}
}
```

### C Guess the Array

> 题意对长度为$N$的数组，可以询问$N$个两个元素之间的和。最后输出这个数组。

又智障啊。。。思路一直是死的，询问1 2/2 3/3 4/../n..1，但其实这样是可以最后推出来的，也就是这样是多解啊。。所以说直接求 12/ 2 3/ 3 1把前三个元素干出来之后，剩余元素也就出来了。。真的智障。。。WA5次，最后才搞出来。



### D T-shirts Distribution

> 每个人穿衣服可以有固定的大小，也可以在两种型号(两种型号必相邻)之间选择一个。现在给出6种衣服的数量，问衣服的分配情况。

水题。。直接从最小的开始贪。。



### E Games on a CD

> 每个名字的长度是相等的，现在给出$g$个字符串，从这$g$个字符串里面选出$n$个，顺时针的情况下可以组成给定字符即可。。

AC自动机，因为长度等长，也不可能有覆盖的情况，所以直接跑一遍，看最终是否有重复+数量够不够。



### F Polycarp's problems

> 给出一个长度为$n$的数组$a\_i$，然后起始的值为$b\_i$，要求在起始的值为$b\_i$的情况下，遇到一个$a\_i$就相加，但不能有小于等于0的情况，问最后要去掉多少个值。现在有m个询问，对于每一个$b\_i$都要求一次最小去掉的数量。

二分取$1$到$n$个所要具备的最小值，然后在结果里面再次二分。



























