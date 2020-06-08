---
title: CodeChef August Lunchtime 2016
date: 2016-09-03 14:54:44
tags: [CodeChef,51nod,树上直径]
categories: 题记
---

做了Codechef上面的[August Lunchtime 2016](https://www.codechef.com/LTIME39)，上面的题都有中文题面，还有解答，还有代码。觉得这个网站也不错呀。。。

然后这次做完发现是三道easy，一道medium。一想也没用什么数据结构，好像是差不多哈，但是第四个树上直径这个和51nod上面树上最远点对是一样的想法，做得时候自己感觉还是挺难的。第三题数组开小了，然而一直返回WA，找了很久。第二个有相同的数字也WA了一个晚上。这样想来觉得也不那么简单了。

代码在[这里](https://github.com/2997ms/My_Algorithm/tree/master/Codechef/August-Lunchtime-2016)。

A题Studying Alphabet不用写了。。。

### B题Lock-Free Stack

------

因为有P = (A1 + 1) × (A2 + 1) × ... × (AN + 1)，然后P<=10^6，所以最多20个进程，那就对每个进程的输出都要符合压栈顺序。(一开始自己一位一位找，发现有相同的数字，所以WA了一晚上，后来还是对每个进程都满足顺序就可以了)。

### C题 Short in Average 

------

这个题自己搞过原题，感触就是平均值问题很容易搞成二分，然后对每一个值减去二分的值，然后去判断。[51nod 1711平均数](http://www.51nod.com/onlineJudge/questionCode.html#!problemId=1711)也是这样，二分这个平均数x，然后每个数减去x，那么这样的话就是算区间和<0的个数与k去相比，区间和[le,ri]小于0，相当于前缀和sum[ri]-sum[le-1]<0，sum[ri]<sum[le-1]，这个不就是逆序对的数目嘛，发现离散化+树状数组就可以做了。

这个题的思路也是这样，每个边减去二分值x，然后直接判环就好了。

### D题 Product of Diameters 

------

这个题目赞在要逆向考虑，往里面加边，然后输出结果。那么往里面加边，就是除以每颗树直径的直径的乘积，这个可以维护，然后求出这两棵树内部的直径，这个维护相当于需要维护一个集合里面的最远距离，想了一下和[51nod 1766树上的最远点对](http://www.51nod.com/onlineJudge/questionCode.html#!problemId=1766)本质上是一样的题目，也是维护一个集合里面距离最远的两个点，然后两个集合的合并，只可能在这四个点里面出，倍增求lca，求距离。

这个题思想还是挺简单的，要想清楚过程，自己写起来一些细节写得还是不够好。