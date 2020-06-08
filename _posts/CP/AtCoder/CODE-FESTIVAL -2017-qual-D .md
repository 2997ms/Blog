---
title: CODE FESTIVAL 2017 qual D 
date: 2017-11-09 20:52:14
tags: [Atcoder]
---

Atcoder选做

<!--more--> 

### CODE FESTIVAL 2017 qual D - Yet Another Palindrome Partitioning

> [题目链接](http://code-festival-2017-qualc.contest.atcoder.jp/tasks/code_festival_2017_qualc_d)
>
> 题意是把一个字符串分成最小数量，使得每一份都可以变成回文串。

dp。dp[i\]表示到位置i的最小分割数。f[x\]表示当前状态是x的最小分割数，如果只记录每一个字母的奇偶的话，那么可以构成回文串sum[i\]^sum[j\]=0||2^i。

```c++
int dp[maxn],f[70000010];
char x[maxn];

void solve()
{
    scanf("%s",x+1);

    int len = strlen(x+1);
    int sum = 0;
    
    memset(f,0x3f,sizeof(f));
    f[0] = 0;
    repp(i,1,len)
    {
        int y = (1<<(int)(x[i]-'a'));
        sum ^= y;
        dp[i] = INF;
        dp[i] = min(dp[i],f[sum]+1);
        rep(j,0,26)
        {
              dp[i] = min(dp[i],f[sum^(1<<j)]+1);
        }
        f[sum] = min(f[sum],dp[i]);
    }
    printf("%d\n", dp[len]);
}
```

