---
title: Codeforces 645 Div2
date: 2020-05-30 15:15:31
tags: [Codeforces]
categories: [题记]
---

2020年还在做div2真是棒棒了呢。

> 我就今年坚持打，我不信到不了的啊。

<!--more-->

> C. Celex Update

C题题意是给出了gaz-giz的那种数字网格，给出起点和终点，每一种走法计算路径上的和，算从起点到终点有多少种和。

![D_1](https://raw.githubusercontent.com/2997ms/My_Algorithm/master/source_pic/CF645/C1.png)

来，我在比赛中干了什么呢？算清楚了给出一个点具体的值。

之后发现就是最大值到最小值的差值，然后我居然就开始考虑最小值怎么算，最大值怎么算。后来发现就算算清楚了也是要爆long long的，心态开始崩。。。

这是结束后后面红旺发给我的。。。。。

![D_1](https://raw.githubusercontent.com/2997ms/My_Algorithm/master/source_pic/CF645/C2.jpg)

我就不清楚我当时在想什么。后续x*y+1也是可以猜出来的，但当时我自己莫名地搞出了一个反例觉得不对，后来是自己反例算错了。。。

```c++
void solve() {
    SLLA(xx1);
    SLLA(yy1);
    SLLA(xx2);
    SLLA(yy2);
 
    ll ans = (xx2 - xx1 ) *(yy2 - yy1) + 1;
    printf("%lld\n", ans);
}
```

结束之后发现D题简单啊，二分一下就结束了，唯一需要注意的是一个地方爆了long long，自己用double给莽过去了。

