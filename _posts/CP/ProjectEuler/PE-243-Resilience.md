---
title: PE#243 Resilience
date: 2018-09-15 10:08:21
tags: [PE]
categories: 题记
---

[题目链接](https://projecteuler.net/problem=243)

<!--more-->

题意是找到一个phi(m)/(m-1) < 15499/94744，要求m最小。
$$
phi(n)=n*\prod_{n|p}(1-\frac{1}{p})
$$

```c++
vector<int> phi;
vector<int> prime;

void sieve(int n) {
  phi.resize(n);
  for (int i = 0; i < n; ++ i) phi[i] = i;
  for (int i = 2; i < n; ++ i) {
  	if (i == phi[i]) {
  		prime.push_back(i);
    	for (int j = i; j < n; j += i) {
      		phi[j] = phi[j] / i * (i - 1);
    	}
  	}
  }
}

ll get_phi(ll x) {
	if(x < phi.size())return phi[x];
	ll ans = x;
	for(ll i=2; i*i<x; i++) {
		if(x%i == 0) {
			ans = ans/i*(i-1);
			while(x%i==0)x/=i;
		}
	}
	if(x!=1) ans=ans/x*(x-1);
	return ans;
}

ll sol(ll a,ll b) {
	ll now = 1;
	for(int i=0;i<prime.size();i++) {
		for(ll k=1;k<prime[i];k++) {
			ll x = get_phi(now*k);
			ll y = now*k-1;
			if(x*b<y*a) return now*k;
		}
		now*=prime[i];
	}
	return 0;
}

void solve() {
	int n = 1000000;
	sieve(n);
	cout << sol(15499, 94744) << endl;
}
```

