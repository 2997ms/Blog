---
title: Hackerrank HourRank 17
date: 2017-02-07 19:03:50
tags: [Hackerrank]
categories: [题记]
---

好久好久没有做题了。。。去年定的目标，自己不算不努力，但还是没能完成。。。今年继续加油吧。。。

过去三个月总把LeetCode刷通了一遍，收获很大。但感觉一遍来说还不是很熟练，时间好紧啊。。。

[题目链接](https://www.hackerrank.com/contests/hourrank-17/challenges)与[代码链接](https://github.com/2997ms/My_Algorithm/tree/master/Hackerrank/HourRank_17)

### The Hurdle Race

> 水题不说了。。。



### Organizing Containers of Balls

> 题意是给出$n(n<100)$个颜色的球，$n$个盒子。每次可以对两个盒子中的一个球做一次交换，问最终能否达到一种状态—所有颜色的球在一个盒子里，一个盒子里只有一个颜色的球。

对所有行和列排个序，判断是否相等。



### GCD Matrix

> 题意是给出集合A、B，集合元素数量$n(n<10^5)$，集合元素的数值范围$x<10^5$。现在两个集合中各取一个元素，问可以得到的GCD的种类。

因为数值范围小于$10^5$，容斥一发。

```c++
int A[maxn], count[maxn], Ma[maxn], n;
for(int i=1; i<=n; i++) 
    count[A[i]] ++;
for(int i=1; i<=100000; i++) {
    int j = i;
    while(j <= 100000) {
        Ma[i] += count[j];
        j += i;
    }
}


int Ma[maxn], Mb[maxn], ans[maxn];
for(int i=100000; i>=1; i--) {
    ans[i] = Ma[i] * Mb[i];
    int j = i;
    while( j <= 100000 ) {
        if(j > i) ans[i] -= ans[j];
        j += i;
    }
}
```



### Number Game on a Tree

> 题意是给出了一个游戏规则，有一堆数，A和B，分别从这里面去除一个数，但是这次去除的数一定要不大于上一次去除的数。不能继续去除数字的人输。然后现在有一颗树，$n$个节点($n<5*10^5$)，将每两个叶子节点路径上的数字作为一个list，问有多少个list可以保证A赢这个游戏，A先手。

首先考虑先手赢的条件，如果每一个数字都是偶数个，那么一定后手赢。因为无论先手去除哪一个，后手照做就好了。那么一旦出现奇数个元素的数字，就一定先手赢，先手把那个元素去除，就变成了前者说的情况了。



现在就考虑有多少个两个叶子节点路径上的数字都是偶数个，考虑异或，把$color$变成$(1<<color)$作为每一个节点上的数值，这样可以保证当路径异或值为0的时候，就一定是偶数个。那么这里又有一个问题变成$(1<<color)$会有溢出的问题，哈希取模。