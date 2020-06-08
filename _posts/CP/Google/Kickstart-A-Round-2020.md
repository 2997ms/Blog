---
title: Kickstart A Round 2020
date: 2020-03-22 18:37:34
tags: [Google]
categories: [题记]
---

今年的Kickstart这一轮比往年简单太多了，简单到我都AK了，昨晚的Leetcode D题我都不会做。。。

榜上的大佬们最快16分钟就AK了。写完题解之后发现我今年真的是越来越菜了 ^ - ^。。。 

<!--more-->

[题目链接](https://codingcompetitions.withgoogle.com/kickstart/round/000000000019ffc7)

### A. Allocation

贪心。

```c++
std::vector<int> v;
void solve() {
    v.clear();
    int n,d;
    SA(n);
    SA(d);
    REPP(i,1,n) {
        int x;
        SA(x);
        v.push_back(x);
    }
    sort(v.begin(), v.end());
    int cnt = 0;
    REP(i,0,v.size()) {
        if(d >= v[i]) {
            d-=v[i];
            cnt = i+1;
        } else {
            break;
        }
    }
    printf("%d\n", cnt);
}
```

### B. Plates

最后题意就是化成了01背包，贪心明显不行。

```c++

int v[55][55];
int dp[55][3005];
void solve() {
    memset(v, 0, sizeof(v));
    memset(dp,0,sizeof(dp));
    int n,k,p;
    SA(n);
    SA(k);
    SA(p);
    REPP(i,1,n) {
        REPP(j,1,k) {
            SA(v[i][j]);
            v[i][j] += v[i][j-1];
        }
    }
    REPP(i,1,n) {
        REPP(s,0,p) {
            dp[i][s] = max(dp[i][s], dp[i-1][s]);
        }
        REPP(s,0,p) {
            dp[i][s] = max(dp[i][s], dp[i-1][s]);
            REPP(j,1,k) {
               if(s + j > p) continue;
               dp[i][s+j] = max(dp[i][s+j], dp[i-1][s] + v[i][j]);
            }
        }
    }
    printf("%d\n", dp[n][p]);
}
```

### C. Workout

二分。

```c++
ll n,k;
ll val[maxn];

bool check(ll mid) {
    ll now = k;
    for(int i=2;i<=n;i++) {
        if(val[i] - val[i-1] <= mid) {
            continue;
        }
        ll sur = val[i] - val[i-1];
        ll need = (sur - 1 )/ mid;
        if(now >= need) {
            now -=need;
        } else {
            return false;
        }
    }
    return true;
}

void solve() {
    SLLA(n);
    SLLA(k);
    ll le = 1;
    ll ri = 0;
    REPP(i,1,n) {
        SLLA(val[i]);
        if(i > 1) {
            ri = max(ri,val[i]-val[i-1]);
        }
    }

    while(le < ri) {
        ll mid = (le + ri)/2;
        if(check(mid)) {
            ri = mid;
        } else {
            le = mid + 1;
        }
    }
    printf("%lld\n", le);

}
```



### D. Bundling

Trie树上，然后贪心。

```c++

ll n, k, cnt;
int ch[maxn][30];
int num[maxn], sub[maxn];
ll ans;
char val[maxn];

void insert(char *x, int len)
{
    int now = 0;
    for (int i = 1; i <= len; i++)
    {
        int k = x[i] - 'A' + 1;
        if (!ch[now][k])
        {
            ++cnt;
            ch[now][k] = cnt;
        }
        now = ch[now][k];
        num[now]++;
    }
}

void dfs(int now, int deep, int fa) {
    REPP(i,1,26) {
        if(ch[now][i] != 0) {
            dfs(ch[now][i], deep + 1, now);
        }
    }

    int sz = num[now] + sub[now];
    if(sz < 0) {
    } else {
        ans += (sz/k)*deep;
        if(fa!=-1) {
            sub[fa] += -(sz/k)*k;
            sub[fa] += sub[now];
        }
    }
}

void solve() {
    ans = 0;
    cnt = 0;
    memset(ch,0, sizeof(ch));
    memset(num, 0, sizeof(num));
    memset(sub, 0, sizeof(sub));
    SLLA(n);
    SLLA(k);
    REPP(i,1,n) {
        scanf("%s", val + 1);
        int len = strlen(val+1);
        insert(val, len);
    }
    dfs(0, 0, -1);
    printf("%lld\n", ans);
}
```

