---
title: CodeChef October Lunchtime 2016
date: 2016-11-05 11:00:34
tags: [CodeChef, Codeforces, 树重心, LCA, sync_with_stdio, 分块, 树分治]
categories: [题记]
---

[题目链接](https://www.codechef.com/LTIME41)与[代码链接](https://github.com/2997ms/My_Algorithm/tree/master/CodeChef/CodeChef-October-Lunchtime-2016)

访问CodeChef好慢啊。。。

### A The Largest Bouquet

>取树叶，要么是同一颜色的，要么是同一棵树上的。问最大值。

矩阵里面同一行同一列的最大值。



### B FastFood Combos 

> 商店里的每个物品有自己的单价，有各种优惠活动，花$P\_i$钱可以购买$Q\_i$个产品，问想要购买所有$N$个产品，最少需要花多少钱。

其实这道题还是要枚举，但是怎么枚举是一个问题。

首先$dp1[x\]$表示只通过单一渠道购买到的(要么单买，要么优惠)，然后这里对优惠下有一个$2^n$枚举，得到各种情况下优惠上的最小值。

```c++
rep(i, 0, n)
    {
        scanf("%lld", &val[i]);
        dp1[1 << i] = val[i];
    }
    dp1[0] = dp2[0] = 0;
    repp(i, 1, m)
    {
        ll mask = 0;
        ll p, q;
        scanf("%lld%lld", &p, &q);
        rep(j, 0, q)
        {
            int x; scanf("%d", &x);
            x--;
            mask |= (1 << x);
        }
        dp1[mask] = min(dp1[mask], (ll)p);
    }
    for (int i = (1 << n) - 1; i >= 0; i--)
    {
        for (int k = 0; k < n; k++)
        {
            if ((i >> k) & 1)
            {
                dp1[i ^ (1 << k)] = min(dp1[i ^ (1 << k)], dp1[i]);
            }
        }
    }
```

$dp2[x\]$表示两种方式混合的最小值，枚举子集也学到了新姿势。。。

```C++
    for (int mask = 0; mask < (1 << n); mask++)
    {
        int submask = (mask - 1)&mask;
        dp2[mask] = dp1[mask];
        do
        {
            dp2[mask] = min(dp2[mask], dp2[submask] + dp1[mask^submask]);
            submask = (submask - 1)&mask;
        }while (submask > 0);
    }
```

最终结果就是$dp2[(1<<n)-1\]$。



### C Restore the Permutation

> $1$到$N$的排列，有两种询问：
>
> $1 \  X \  Y  \  D |A\_x-A\_y|能否被D整除$
>
> $2 \  X \  Y  \  X是否小于Y$
>
> 最多可以提出$N\*13$个1类询问，以及$N-1$个2类询问，要求还原数列。

好难啊。。。$13*N$。。。$N$的范围是$10^4$级别的，很明显是要$log$一下，但是怎么分治呢。。。

分治，先把所有数分成%2=1的，以及%2=0的，之后对%2=0的那部分%4又有两种情况，按照这个分类有什么好处呢，就是比较每个序列的最小值，最后可以直接*2还原。



### D The Counties

> 给出一棵树，这棵树上的点隶属于某个国家，有$Q$个询问，每次问距离某一点$V$最近的一个国家$C$的城市是多远。

如果是在$10^5$级别，[CF_342E](http://codeforces.com/problemset/problem/342/E)给出了一个分块的。

这个题离线解，树分治，每次找树的重心，深搜一遍，如果是在不同的子树上，每次的结果最小值就是国家$C$在子树上的最小深度与该城市的最小深度之和。如果是在相同的子树上，递归求解。