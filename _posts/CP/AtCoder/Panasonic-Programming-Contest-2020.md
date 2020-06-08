---
title: Panasonic Programming Contest 2020
date: 2020-03-21 21:54:18
tags: [Atcoder, 思维题]
categories: [题记]
---

最近是有打了几场Atcoder的，这几天统一还是要补一下的。距离我上一次更新题解也是够久的了。

<!--more-->

几乎所有的Atcoder题目都可以被归类为【思维题】。数据结构在我认知的范围之内都不复杂，但你就是想不出来。

自己写这个blog也真是忘了初衷了，一开始每一篇都写的那么认真，你看看现在。

这场主要还是E、F题，A到D题还是比较水的。

[题目链接](https://atcoder.jp/contests/panasonic2020/tasks)

### E - Three Substrings

三个字符串，是一个字符串的子串。随意组合，要求原字符串长度最小。

这题就不能什么枚举组合求贪心，当时比赛的时候就已经发现不能贪心了。然而。。。我居然还各种特判条件，然后怎么弄都不对。。。真的是智障。。。

直接枚举ab，ac，bc偏移，然后最后统一组合。

```c++
 
int ab[maxn], ac[maxn], bc[maxn];
 
string aa, bb, cc;
 
void solve() {
    cin >> aa >> bb >> cc;
    int mid = 1e5;
 
    REP(i,0,aa.length()) {
        REP(j,0,bb.length()) {
            if(!(aa[i] == bb[j] || aa[i] == '?' || bb[j] == '?')) {
                ab[i - j + mid] = 1;
            }
        }
   }
 
   REP(i,0,aa.length()) {
        REP(j,0,cc.length()) {
            if(!(aa[i] == cc[j] || aa[i] == '?' || cc[j] == '?')) {
                ac[i - j + mid] = 1;
            }
        }
   }
 
   REP(i,0,bb.length()) {
        REP(j,0,cc.length()) {
            if(!(bb[i] == cc[j] || bb[i] == '?' || cc[j] == '?')) {
                bc[i - j + mid] = 1;
            }
        }
   }
 
   int ans = aa.length() + bb.length() + cc.length();
   REPP(i,-4000, 4000) {
    REPP(j, -4000, 4000) {
        if(!ab[i + mid] && !ac[j + mid] && !bc[j - i + mid]) {
            int minn = min({0, i, j});
            int maxx = max({(int)aa.length(), (int)bb.length() + i, (int)cc.length() + j});
            ans = min(ans, maxx - minn );
        }
    }
   }
   cout<<ans<<endl;
 
}
```

### F - Fractal Shortest Path

F题的题意是一张$3^m * 3^m$大图，中间是block的情况。然后给你两个点，求距离。

多么简明的题意！但为什么就是想不出来呢？

递归，如果发现不在同一层，直接曼哈顿距离。

在同一层，如果是中间那一层，考虑是上面还是下面过去。

否则，再往下递归就行了。

```c++
ll wid[maxn];
ll dfs(ll x1, ll y1, ll x2, ll y2, int level) {
    if (level == 0) {
        return abs(y1 - y2);
    }
    ll w = wid[level-1];
    if(x1 / w != x2 / w) {
        return abs(x1 - x2) + abs(y1 - y2);
    }
    if(x1 / w == 1 && abs(y1 / w - y2 /w) >= 2) {
        return abs( y1 - y2 )  + min(x1 % w + 1 + x2 % w + 1, w - x1%w + w - x2%w);
    }
    return dfs(x1 % w, y1, x2 % w, y2, level-1);
}
 
int q;
void solve() {
    wid[0] = 1;
    REPP(i,1,30) {
        wid[i] = wid[i-1] * 3;
     }
    SA(q);
    REPP(i,1,q) {
        ll a1,b1,c1,d1;
        SLLA(a1);
        SLLA(b1);
        SLLA(c1);
        SLLA(d1);
        a1--;
        b1--;
        c1--;
        d1--;
        if(abs(a1 - c1) > abs(b1 - d1)) {
            swap(b1, a1);
            swap(c1, d1);
        }

        cout<< dfs(a1, b1, c1, d1, 30) <<endl;
    }
}
```