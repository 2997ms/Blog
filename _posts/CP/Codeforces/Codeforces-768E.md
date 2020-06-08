---
title: Codeforces 768E
date: 2020-05-16 13:19:54
tags:
---

智障计划第一弹，又来一个计划，都不知道有没有第二弹。

<!--more-->

![D_1](https://raw.githubusercontent.com/2997ms/My_Algorithm/master/source_pic/zhizhang.png)

题意跟经典的nim博弈的区别就在于移动过的石堆不能再被移动了。

一开始我的想法就是直接判断n的奇偶性就行了？

发现题意读错了，是同一个数字不能在一个堆里取了。

可以打表找一下sg函数的规律。在打表sg的时候暴力 + dp。

```c++
int vis[1005];
int sg[22][1<<21];

int mex() {
    for(int i = 0; ; i++) if(!vis[i]) return i;
}

void pre() {
    memset(sg, 0, sizeof(sg));
    REPP(i,1,20) {
        sg[i][0] = 0;
        for(int j = 1; j < (1<<20) ; j++) {
            memset(vis, 0, sizeof(vis));
            for(int k = 0; k < i; k++) if ((j >> k) &1)
                    vis[sg[i - k - 1][j ^ (1 << k)]] = 1;
            sg[i][j] = mex();
            // cout<<i<<" "<<k<<" "<<sg[i][k]<<endl;
        }
    }
    REPP(i,0,20) {
        printf("%d %d\n", i, sg[i][(1 << i) - 1]);
    }
}

int main()
{   
    //pre();
    int n;
    SA(n);
    int cnt = 1;
    int ans = 0;
    REPP(i,1,n) {
        int x;
        SA(x);
        for(cnt=1;cnt*(cnt+1)/2<=x;cnt++);
        ans ^= (cnt-1);
    }
    if(ans) {
        puts("YES");
    } else {
        puts("NO");
    }
    return 0;
 
}
```

