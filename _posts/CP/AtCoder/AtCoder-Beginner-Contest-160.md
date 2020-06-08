---
title: AtCoder Beginner Contest 160
date: 2020-04-12 22:15:43
tags: [AtCoder, dp, 树]
categories: [题记]
---

退化。

<!--more-->

[题目链接](https://atcoder.jp/contests/abc160)

#### F Distributing Integers

> 题意是给出一棵树，指定一个点开始染色，之后染色的点必须与已经染过色的点相邻才可以被染。问每个点作为第一个染色点的方案树。

假定root=1，总的方案数为
$$
\frac{n!}{\sum_{k=1..n}{size[k]}}
$$
之后就是根据已有的root的答案对子树进行转换。

```C++
int n;
std::vector<int> edge[maxn];
ll fac[maxn], inv_fac[maxn];
ll res[maxn];
ll sz[maxn], mul[maxn];

void dfs(int root, int fa) {
    sz[root] = 1;
    mul[root] = 1;
    REP(i,0,edge[root].size()) {
        int nxt = edge[root][i];
        if(nxt == fa) {
            continue;
        }
        dfs(nxt, root);
        sz[root] += sz[nxt];
        mul[root] = mul[root] * mul[nxt] % mod;
    }
    mul[root] = mul[root] * sz[root] % mod;
}

void dfs2(int root, int fa, ll premul) {
    ll k = premul;

    REP(i,0,edge[root].size()) {
        int nxt = edge[root][i];
        if(nxt == fa) {
            continue;
        }
        k = k * mul[nxt] % mod;
    }
    res[root] = fac[n - 1] * po(k, mod-2,mod) % mod;
    REP(i,0,edge[root].size()) {
        int nxt = edge[root][i];
        if(nxt == fa) {
            continue;
        }
        dfs2(nxt, root, k * po(mul[nxt], mod -2, mod) % mod * (n - sz[nxt])%mod);
    }
}

void solve() {
    SA(n);
    REPP(i,1,n-1) {
        int a, b;
        SA(a);
        SA(b);
        edge[a].push_back(b);
        edge[b].push_back(a);
    }
    fac[0] = 1;
    inv_fac[0] = 1;
    REPP(i,1,n) {
        fac[i] = fac[i-1] * i % mod;
        inv_fac[i] = po(fac[i], mod - 2, mod);
    }
    dfs(1, -1);
    dfs2(1, -1, 1);
    REPP(i,1,n) {
        printf("%lld\n", res[i]);
    }
}
```

