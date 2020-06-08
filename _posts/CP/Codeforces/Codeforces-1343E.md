---
title: Codeforces 1343E
date: 2020-05-16 14:31:54
tags:
---

一道题从读题到写出来一个半小时过去了。

<!--more-->

题意是重新分配边的权值，使得a->b->c的权值最小。

没有思路。

每次都想着记录边，但是边在最段路中记录起来消耗太大了。考虑更换起点，得到三个dis的数组。这里的思路很多题都是这么干的了。

然后枚举中间的点。这里确实没有想到。

```c++
struct edge {
  int to, cost;
};

vector<int> dijkstra(const vector<vector<edge>> &g, int s) {
  vector<int> d(g.size() + 5, INF);
  priority_queue<pair<ll,ll>, vector<pair<ll,ll>>, greater<pair<ll,ll>>> que;
  d[s] = 0;
  que.emplace(0, s);
  while (!que.empty()) {
    auto p = que.top();
    que.pop();
    int u = p.second;
    if (d[u] < p.first) continue;
    for (auto &e : g[u]) {
      int v = e.to;
      if (d[v] > d[u] + e.cost) {
        d[v] = d[u] + e.cost;
        que.emplace(d[v], v);
      }
    }
  }
  return d;
}

ll n,m,a,b,c;
ll p[maxn];
vector<vector<edge>> g;
void solve() {
    SLLA(n);
    SLLA(m);
    SLLA(a);
    SLLA(b);
    SLLA(c);
    g.resize(n+5);
    REPP(i,1,n) {
        g[i].clear();
    }
    REPP(i,1,m) {
        SLLA(p[i]);
    }
    sort(p+1,p+m+1);
    REPP(i,1,m) {
        p[i] += p[i-1];
    }
    REPP(i,1,m) {
        int h,f;
        SA(h);
        SA(f);
        g[h].push_back(edge{f, 1});
        g[f].push_back(edge{h, 1});
    }

    std::vector<int> dis1 = dijkstra(g, a);
    std::vector<int> dis2 = dijkstra(g, b);
    std::vector<int> dis3 = dijkstra(g, c);
    ll ans = LL_INF;
    REPP(i,1,n) {
        if(dis1[i] + dis2[i] + dis3[i] > m) {
            continue;
        }
        ans = min(ans, p[dis1[i] + dis2[i] + dis3[i]] + p[dis2[i]]);
    }
    printf("%lld\n", ans);
}
```

