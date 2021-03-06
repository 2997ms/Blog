---
title: AtCoder Grand Contest 002
date: 2016-09-07 16:32:28
tags: [AtCoder,整体二分,DP]
categories: 题记 
---

[题目链接](http://agc002.contest.atcoder.jp/)，这场D题没接触过，整体二分，挺典型。E和F做得我好头痛，E题提交了好多次，F题那个DP。。。都很神。

[代码链接](https://github.com/2997ms/My_Algorithm/tree/master/AtCoder/Grand_Contest_002)

### A Range Product

------

题是给定一个范围，问乘积是正是负。。。。。。





### B Box and Ball 

-----

题意是给出了n个盒子，第1个盒子里面有红球，其余的都有白球。现在有m次操作，每次从第x个盒子里面随机抽一个球，放到y盒子里面。问最终有多少个盒子可能会有红球。

有染的一律算上，注意盒子里没有球的情况。





### C Knot Puzzle

-------

题意是有n段绳子，每段绳子有一个长度。现在这n段绳子连在一起，Snuke想把它拆开，要求是选择一段长度至少为L的，才能拆成两段。问是否能把这一段绳子拆成n段，给出策略。

这个题一开始我是贪心从两头开始拆，发现是错的，比如说这种情况：

4 6
3 3 1 4

还是选出相邻和的最大值，满足>=L的，就以这一段为最后一段。





### D Stamp Rally

------

题意是给出一个无向联通图，然后有q次询问x,y,z，问找出x和y连通的节点数量在哪一条边之后，数量大于等于z。

当时做的时候问题看起来特别唬人，其实对于每一次查询就是看哪一次添加边之后，两者的并查集数量之和大于z了。那么q次询问，就用到整体二分的思想，把mid存到vector里，然后整体一起跑。每次不断缩小范围。





### E Candy Piles

-------

题意是现在有n摞球(原题是糖，说成球比较好理解图。。。)，双方有两种操作，一种是选最多那摞球，吃掉。。。另一种是对每一摞球吃一个。最后吃球的人输。先给出各摞球的个数，问最终谁赢。

这题很神。。。完全见过类似的。

如果我们把球按照从大到小排好会发现是这样的，

![image](https://raw.githubusercontent.com/2997ms/My_Algorithm/master/source_pic/AGC_002/E1.png)

那这样的话两种操作就是这样的：

![image](https://raw.githubusercontent.com/2997ms/My_Algorithm/master/source_pic/AGC_002/E2.png)

之后呢，问题就可以变成了你从左下边的点开始走，走到边界停止，就像这样：

![image](https://raw.githubusercontent.com/2997ms/My_Algorithm/master/source_pic/AGC_002/E3.png)

对于边界来说，是胜利状态，标⭕️。失败状态，标❌。那么一个点(x,y)的可达状态是(x+1,y),(x,y+1),根据这些可以画出这幅图每个点的胜负情况。

![image](https://raw.githubusercontent.com/2997ms/My_Algorithm/master/source_pic/AGC_002/E4.png)

现在就是要求左下点(1,1)点究竟是❌还是⭕️。

![image](https://raw.githubusercontent.com/2997ms/My_Algorithm/master/source_pic/AGC_002/E5.png)

根据图7，分析可知就是要找拐点到y=x这条线的距离奇偶，推得偶数不能给到胜利状态。最后注意一种情况就是所有边界值都在y=x的直线之上，这个时候只有一个拐点，就没有right值，只有up值。







### F Leftmost Ball

------

题意是有n种颜色的球，颜色分别为1到n，每一种颜色的球有k个，现在把这些球排成一排，把每一种颜色的球的最左端的那个球，染成颜色0。问最终球的排列有多少种情况。

这个dp也好神。。。。

![image](https://raw.githubusercontent.com/2997ms/My_Algorithm/master/source_pic/AGC_002/F1.png)

非常奇妙地把这n*k个球转换成了一个图，灰色的球代表颜色0，然后问这个图拓扑排序能够有多少种。(ORZ....)

接下来有多少种呢。。。又使用dp来解(ORZ....)

![image](https://raw.githubusercontent.com/2997ms/My_Algorithm/master/source_pic/AGC_002/F2.png)

对于每一个情形dp[i\][j\]来说，有两种选择：1.拿一个灰球dp[i-1\][j\]。2.拿一个橙色的球排在当前位置的第一个，那剩下的k-2的球的位置就任意了,即C(i+j\*(k-1)-1，k-2)\*dp[i\][j-1]。

最后将结果乘以n!