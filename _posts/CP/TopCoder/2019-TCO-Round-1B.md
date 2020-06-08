---
title: 2019 TCO Round 1B
date: 2019-05-02 00:25:31
tags: [TopCoder, DP]
categories: [题记]
---

Topcoder上有意思的dp题还是多，但是如果能有更多的editor就好了(你要干嘛。。)

<!--more-->

[题解链接]([https://www.topcoder.com/blog/2019-topcoder-open-algorithm-round-1b-editorials/](https://www.topcoder.com/blog/2019-topcoder-open-algorithm-round-1b-editorials/))

## EllysSki

250的题太简单了。

## EllysTeleport

500的题目是我当初面某司的题，就是有可能碰到环路。

2->1->5->6->7->8->5这种情况。

自己过后想了一下，先处理一下这个点是否在环路上，可以记录这个环路的长度。

之后再扫一遍就完了。

就目前处理问题这里完全不会break down一下。智力问题。

当然我这里直接$N^2$了。

```C++
ll val[100005],nxt[100005];
int fa[100005];

class EllysTeleport {
public:
   
    int getMax(int N, int H1, int A, int B, int P, int Q, int M) {
        val[1] = H1;
        repp(i,2,N) {
            val[i] = (val[i-1]*A+B)%M;
        }
        sort(val+1,val+N+1);
        repp(i,1,N) {
            nxt[i] = i-1;
        }
        int ans = 0;
        memset(fa,-1,sizeof(fa));
        repp(i,1,N) {
            int cnt = 0;
            int x = i;
            while(x!=0&&fa[x]!=i) {
                ++cnt;
                fa[x]=i;
                x=nxt[x];
            }
            ans = max(ans,cnt);
        }
        return ans;
    }
}t;
```

## EllysPearls

1000的题意是每个点有个颜色，要求同一个颜色的点都放在一起。问当前这些点需要更改顺序多少次。

题解里说了一般出现15这种，就考虑bitmask dp。然后这个dp其实很经典了。就是[0,1]表示当前这种颜色是否已经加到集合里了，是否考虑了，然后记录一个最后位置的颜色，就结束了。

```C++
int n;
vector<int>v;
int dp[55][16][1<<16];
class EllysPearls {
    public:

    int dfs(int ind,int col, int state) {

        if(ind>=n) {
            return 0;
        }
        if(dp[ind][col][state]!=-1) {
            return dp[ind][col][state];
        }
        int ans = 1 + dfs(ind+1,col,state);
        if(v[ind] == col) {
            ans = min(ans, dfs(ind+1,col,state));
        }
        if(!((1<<v[ind]) & state)) {
            ans = min(ans, dfs(ind+1,v[ind],((1<<v[ind])|state)));
        }
        dp[ind][col][state]=ans;
        return ans;
    }

    int getMin(int N, int M, vector <int> pearls) {
        memset(dp,-1,sizeof(dp));
        n = N;
        v = pearls;
        return dfs(0,M+1,0);
    }
}t;
```

最近这边正好是五一假期，自己没有回家。十分想老爸老妈，想想这些年自己独立做的这些决定。也不知道该说些什么。只希望自己要一直努力下去，真的请一直一直努力下去。

总有一天，要让他们。。。为我骄傲。。。









