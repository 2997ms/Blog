---
title: 51nod填坑记(二)(1048, 1167)
date: 2018-12-02 23:45:32
tags: [51nod, DP]
categories: 题记
---

前一个还是2017年的事情，51nod填坑记二居然相差了一年多。主要是51nod这一年好像近期才做了一个很大的改变，之前bug太多了啊。。。

这一年自己经历了很多，然而进步很小啊。。。心态有的时候也需要调整。

<!--more-->

### [**51nod 1048**](http://www.51nod.com/Challenge/Problem.html#!#problemId=1048)

本来是$g[i\][le\][ri\]$表示$2^i$用$2^{le}，2^{ri}$表示的种类数，这个时候可以得到。

$g[i\][le\][ri\] = \sum_{k=le}^{ri}g[i-1\][le\][k\] * g[i-1\][k\][ri\]$

然后发现$g[i\][le\][ri\] = g[i-k\][le-k\][ri-k\]$所以可以省略一维。变成

$g[i\][j\] = \sum_{k=0}^{j}g[i-1\][k\] * g[i-1-k\][j-k\]$

同理，$f[i\][j\]$表示前$i$个$1$处理之后，最大的是$2^j$时的数量。可有：

$f[i\][j\] = \sum_{k=0}^{j}f[i-1\][k\] * g[i-k\][j-k\]$

另外，很想吐槽，这里为什么不能mod，一定要上高精度，没办法用的java，然后还卡常。

```java
import java.util.Scanner;
import java.io.*;
import java.math.*;
import java.math.BigInteger;

public class Main
{
    public static void main(String[] args)
    {
        Scanner cin = new Scanner(System.in);
        BigInteger N = cin.nextBigInteger();
        BigInteger f[][] = new BigInteger[155][155];
        BigInteger g[][] = new BigInteger[155][155];
        
        for (int i=0;i<=120;i++) {
            for (int j=0;j<=120;j++) {
                f[i][j] = BigInteger.ZERO; 
                g[i][j] = BigInteger.ZERO; 
            }
        }
        f[0][0] = BigInteger.ONE;

        for (int i=1;i<=120;i++) {
            f[i][i] = BigInteger.ONE;
            for (int j=0;j<i;j++) {
                for (int k=0;k<=j;k++) {
                    f[i][j] = f[i][j].add(f[i-1][k].multiply(f[i-1-k][j-k]));
                }
            }
        }
        BigInteger Two = BigInteger.ONE.add(BigInteger.ONE);
        int tot = 0;
        for (int i=0;i<=120;i++) {
            if (N.mod(Two).toString().charAt(0) == '1') {
                tot++;
                if(tot==1) {
                    for (int j=0;j<=i;j++) {
                        g[1][j] = f[i][j];
                    }
                } else {
                    for (int j=0;j<=i;j++) {
                        for(int k=0;k<=j;k++) {
                            g[tot][j] = g[tot][j].add(g[tot-1][k].multiply(f[i-k][j-k]));
                        }
                    }
                }
            }
            N=N.divide(Two);
        }
        BigInteger ans=BigInteger.ZERO;

        for (int i=0;i<=120;i++) {
            ans = ans.add(g[tot][i]);
        }
         System.out.println(ans.toString());
    }
}

```



### [51nod 1167](http://www.51nod.com/Challenge/Problem.html#!#problemId=1167)

1048的加强版。从01差别到每个位有k种可能。实际上是在最后的DP阶段需要多合并k次。

```java
import java.util.Scanner;
import java.io.*;
import java.math.*;
import java.math.BigInteger;

public class Main
{
    public static void main(String[] args)
    {
        Scanner cin = new Scanner(System.in);
        BigInteger tmp1 = cin.nextBigInteger();
        String tm = tmp1.toString();
        int K =Integer.valueOf(tm);
        BigInteger N = cin.nextBigInteger();
        BigInteger f[][] = new BigInteger[155][155];
        BigInteger g[][] = new BigInteger[155][155];
        BigInteger h[][] = new BigInteger[155][155];
        int maxn = 50;
        for (int i=0;i<=maxn;i++) {
            for (int j=0;j<=maxn;j++) {
                f[i][j] = BigInteger.ZERO;
                g[i][j] = BigInteger.ZERO;
            }
        }
        f[0][0] = BigInteger.ONE;

        for (int i=1;i<=maxn;i++) {
            for(int j=0;j<=maxn;j++) {
                for(int k=0;k<=maxn;k++) {
                    g[j][k] = BigInteger.ZERO;
                }
            }
            for(int j=0;j<=i-1;j++) {
                g[1][j] = f[i-1][j];
            }
            for(int j=2;j<=K;j++) {
                for(int k=0;k<i;k++) {
                    for(int x=0;x<=k;x++) {
                        g[j][k] = g[j][k].add(g[j-1][x].multiply(f[i-1-x][k-x]));
                    }
                }
            }

            for(int j=0;j<i;j++) {
                f[i][j] = g[K][j];
            }
            f[i][i]=BigInteger.ONE;
        }
        int tot = 0;
        int wz = 0;
        g[0][0] = BigInteger.ONE;
        for (int i=0;i<=maxn;i++) {
            String tmp = N.mod(tmp1).toString();
            int s =Integer.valueOf(tmp);
    
            for(int x=0;x<=maxn;x++) {
                for (int j=0;j<=maxn;j++) {
                     h[x][j] = BigInteger.ZERO;
                }
            }
            for(int j=0;j<=i;j++) h[0][j]=g[i][j];
            for(int x=1;x<=s;x++) {
                for (int j=0;j<=i;j++) {
                    h[x][j] = BigInteger.ZERO;
                    for (int k=0;k<=j;k++) {
                        h[x][j]=h[x][j].add(h[x-1][k].multiply(f[i-k][j-k]));
                    }
                }
            }
             for(int x=0;x<=maxn;x++) {
                for (int j=0;j<=maxn;j++) {
                     g[x][j] = BigInteger.ZERO;
                }
            }
            for (int j=0;j<=i;j++) g[i+1][j]=h[s][j];
            N=N.divide(tmp1);
            if(N.equals(BigInteger.ZERO)) {
                wz=i+1;
                break;
            }
        }

        BigInteger ans=BigInteger.ZERO;
        for (int i=0;i<=maxn;i++) {
            ans = ans.add(g[wz][i]);
        }
        System.out.println(ans.toString());
    }
}

```

