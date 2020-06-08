---
title: Codeforces 1355E
date: 2020-05-17 10:45:54
tags: [三分]
---

这题是很标准的三分了，一段区间上求极值。

<!--more-->

然而自己在搞的什么蛇皮啊，在最后一分钟终于搞过了。终于又一次回到紫名了，今年想冲2100，如果可以稳定下来，这个并非没有可能。

![lastMinute](https://raw.githubusercontent.com/2997ms/My_Algorithm/master/source_pic/lastMinute.png)

这尼玛cf就是靠莽啊，敢想敢做敢尝试？？？D题也是莽了一个我自己都不知道什么东西的做法提交居然过了，本来以为我这水平两题收场了，结果最后一小时居然过了三个题。

写了个假算法版本，也过了。就是分析交换的时候的可能，只有三个位置可以做交换，sum/n，如果取模不为0的话再考虑两个位置即可。

```c++
ll n,a,r,m;
ll val[maxn];
ll pre[maxn], t1[maxn],t2[maxn];
void solve() {
    SLLA(n);
    SLLA(a);
    SLLA(r);
    SLLA(m);
    REPP(i,1,n) {
        SLLA(val[i]);
        t1[i] = val[i];
        t2[i] = val[i];
    }
    sort(val+1,val+1+n);
    sort(t1+1,t1+1+n);
    sort(t2+1,t2+1+n);
    REPP(i,1,n) {
        pre[i] = pre[i-1] + val[i];
    }
 
    ll A = a;
    ll R = r;
    ll ans = LL_INF;
    REPP(i,1,n) {
        ll fix = val[i];
 
        ll p = pre[i-1];
        ll need = (i-1)*fix - p;
 
        ll suf = pre[n] - pre[i];
        ll nedre = suf - (n-i) * fix;
 
        ll tmp = need*A + nedre*R;
        ans = min(ans, tmp);
 
        ll cnt = min(need, nedre);
        
        need -= cnt;
        nedre -= cnt;
        tmp = cnt*m + need*A + nedre*R;
        ans = min(ans, tmp);
    }
    if(pre[n] % n == 0) {
        ll s = 0;
        REPP(i,1,n) {
            s += abs(pre[n]/n - val[i]);
        }
        ans = min(ans, s/2*m);
    } else {
       ll sur = pre[n]%n;
       ll anst1 = sur*R;
       ll cn = sur;
       ll ind = n;
              ll sum = pre[n];
       sum -= sur;
       while(cn>=1) {
        while(cn>=1 && t1[ind] > sum/n) {
            t1[ind]--;
            cn--;
        }
        ind--;
       }
 
       ll tmp = 0;
       REPP(i,1,n) {
            tmp += abs(sum/n - t1[i]);
       }
       ans = min(ans, tmp/2*m+anst1);

       ll sur_sum = n - pre[n]%n;
       sum = pre[n] + sur_sum;
       int cntt = 0;
       int i = 1;
       while(cntt < sur_sum) {
        while(t2[i] < sum/n && cntt < sur_sum) {
            cntt++;
            t2[i]++;
        }
        i++;
       }
       ll anst2 = sur_sum*A;
       tmp = 0;
       REPP(i,1,n) {
            tmp += abs(sum/n - t2[i]);
       }
       ans = min(ans, tmp/2*m + anst2);
    }
    printf("%lld\n", ans);
}

```

三分做法，取自Q神quailty：

```c++
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const int MAXN=100005;
int h[MAXN];
int main()
{
    int n,A,R,M;
    scanf("%d%d%d%d",&n,&A,&R,&M);
    M=min(M,A+R);
    for(int i=1;i<=n;i++)
        scanf("%d",&h[i]);
    auto f=[&](int t)->ll
    {
        ll cnt_a=0,cnt_r=0;
        for(int i=1;i<=n;i++)
        {
            if(h[i]<t)cnt_a+=t-h[i];
            else cnt_r+=h[i]-t;
        }
        ll cnt_m=min(cnt_a,cnt_r);
        cnt_a-=cnt_m,cnt_r-=cnt_m;
        return A*cnt_a+R*cnt_r+M*cnt_m;
    };
    int tl=0,tr=1000000000;
    while(tl<tr)
    {
        int tm1=tl+(tr-tl)/3;
        int tm2=tl+(2*tr-2*tl+2)/3;
        if(f(tm1)>f(tm2))tl=tm1+1;
        else tr=tm2-1;
    }
    return 0*printf("%lld\n",f(tl));
}
```



