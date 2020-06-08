---
title: 'PE#12 Highly divisible triangular number'
date: 2018-09-05 21:29:38
tags: [PE]
categories: 题记
---

[题目链接](https://projecteuler.net/problem=12)

<!--more-->

题意就是在n*(n+1)/2的数列中找到最小的，满足因子数目大于500的数字。

```C++
void solve() {
	static int val[50005];
	memset(val,0,sizeof(val));

	int n =50000;
	for (int i=1;i<=n;i++) {
		for (int j=i;j<=n;j+=i) {
			val[j]++;
		}
	}
	for (ll i=1;i<=n;i++) {
		ll v;
		if (i&1) {
			v = val[(i+1)/2]*val[i];
		} else {
			v = val[i/2] * val[i+1];
		}
		if(v>=500) {
			cout<<i*(i+1)/2<<endl;
			return;
		}
	}
}
```

