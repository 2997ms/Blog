---
title: Google Code Jam 2019 Round 1A
date: 2019-04-13 20:27:05
tags: [Google, 中国剩余定理]
categories: [题记]
---

距离2017年竟然也都这么久了。

<!--more-->

[题目链接](https://codingcompetitions.withgoogle.com/codejam/round/0000000000051635)

读英文题目还是捉急。太菜了太菜了。

### Pylons

题意是给出一个r\*c的格子，要求移动的时候不能同行同列对角线，可以任意选起点。问行走的方案。

random_shuffle(vector)

```C++
int r,c;
int con[505][505];
void solve(){
    memset(con,0,sizeof(con));
    sa(r),sa(c);
    repp(i,1,r) {
        repp(j,1,c) {
            repp(x,1,r) {
                repp(y,1,c) {
                    if(i==x||j==y)continue;
                    if(i+j==x+y)continue;
                    if(i-j==x-y)continue;
                    int f1 = (i-1)*c + j-1;
                    int f2 = (x-1)*c + y-1;
                    con[f1][f2]=1;
                }
            }
        }
    }
    int n = r*c;
    vector<int>v;
    rep(i,0,n) {
        v.push_back(i);
    }
    repp(i,1,1000) {
        random_shuffle(v.begin(),v.end());
        int flag=0;
        rep(i,0,n-1) {
            int k = i+1;
            for(;k<n;k++) {
                if(con[v[i]][v[k]]) {
                    swap(v[k],v[i+1]);
                    break;
                }
            }
            if(k>=n) {
                flag = 1;
            }
        }
        if(flag == 0){
            cout<<"POSSIBLE"<<endl;
            rep(i,0,n) {
                int x = v[i]/c + 1;
                int y = v[i]%c + 1;
                cout<<x<<" "<<y<<endl;
            }
            return;
        }
    }
    cout<<"IMPOSSIBLE"<<endl;
}
```

### Golf Gophers

中国剩余定理check，只需要相对互质。

```c++
int modval[7] = {16,11,5,7,9,13,17};
int res[20];
int t,m,n;


void solve() {
    memset(res,0,sizeof(res));
    for(int i=0;i<7;i++) {
        for(int j=0;j<18;j++) {
            printf("%d%c", modval[i], " \n"[j==17]);
        }
        fflush(stdout);
        for(int j=0;j<18;j++) {
            int tmp;
            sa(tmp);
            res[i]+=tmp;
        }
        res[i]%=modval[i];
    }
    repp(i,1,m) {
        int flag = 1;
        rep(j,0,7) {
            if((i%modval[j]) != res[j]) {
                flag = 0;
                break;
            }
        }
        if(flag) {
            printf("%d\n",i);
            fflush(stdout);
            return;
        }
    }
}
```

### Alien Rhyme

建一个trie。(死在题意)

```C++
int cnt;
int val[maxn][30],c[maxn];
map<int,int>f;
int g[maxn];
void insert(string x) {
    int len = x.length();
    int root = 0;
    for(int i=0;i<len;i++) {
        int k = x[i] - 'A';
        if(val[root][k] == -1) {
            ++cnt;
            val[root][k]=cnt;
        }
        root=val[root][k];
    }
    c[root]++;
}

int n;
int ans;
void dfs(int root) {
    for(int k=0;k<26;k++) {
        if(val[root][k] != -1) {
            dfs(val[root][k]);
            c[root] += c[val[root][k]];
        }
    }
    if(c[root]>=2&&root!=0){
        c[root]-=2;
        ans+=2;
    }
}

void solve(){
    memset(val,-1,sizeof(val));
    memset(c,0,sizeof(c));
    memset(g,0,sizeof(g));
    f.clear();
    cnt = 0;

    cin>>n;
    rep(i,0,n) {
        string x;
        cin>>x;
        reverse(x.begin(),x.end());
        insert(x);
    }
    ans = 0;
    dfs(0);
    cout<<ans<<endl;
}
```

