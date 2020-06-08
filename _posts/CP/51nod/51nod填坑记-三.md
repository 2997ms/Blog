---
title: 51nod填坑记(三)(1203,1695)
date: 2018-12-09 01:11:51
tags: [51nod]
categories: 题记
---

现在居然一点多了。。。不总结题就是没做过这个题。

<!--more-->

### [51nod 1203](http://www.51nod.com/Challenge/Problem.html#!#problemId=1203)

通过这个题，重新复习了一下莫队，好多都忘了。

质数<230的可以用RMQ，发现就只有50个。

质数>230的发现只有可能0或者1，莫队。

```C++
int num[maxn];
struct RMQ {
    int f_min[maxn][20],f_max[maxn][20];
     
    void init(int n)
    {
        for(int i = 1; i <= n; i++)
        {
            f_min[i][0] = f_max[i][0] = num[i];
        }

    
        for(int i = 1; (1<<i) <= n; i++)  //按区间长度递增顺序递推
        {
            for(int j = 1; j+(1<<i)-1 <= n; j++)  //区间起点
            {
                f_max[j][i] = max(f_max[j][i-1],f_max[j+(1<<(i-1))][i-1]);
                f_min[j][i] = min(f_min[j][i-1],f_min[j+(1<<(i-1))][i-1]);
            }
        }
    
    }
     
    int query_max(int l,int r)
    {
        int k = (int)(log(double(r-l+1))/log((double)2));
        return max(f_max[l][k], f_max[r-(1<<k)+1][k]);
    }
     
    int query_min(int l,int r)
    {
        int k = (int)(log(double(r-l+1))/log((double)2));
        return min(f_min[l][k], f_min[r-(1<<k)+1][k]);
    }
}rmq;

ll vis[maxn];
vector<int>v;
map<int,int>ind;
map<int,int>an_ind;
ll val[maxn],ni[maxn];

struct Q{
    int le;
    int ri;
    int id;
}q[maxn];

ll ans[maxn];
int pos[maxn];
bool cmp(Q & a, Q & b) {
    return pos[a.le]<pos[b.le] || (pos[a.le]==pos[b.le] && a.ri<b.ri); 
}
int last[maxn];
int v_le[maxn],v_ri[maxn];
vector<int>pri;
void solve() {
    int up = 230;
    int cnt = 0;
    repp(i,2,up) {
        if(vis[i])continue;
        pri.push_back(i);
        for(int j=i;j<=up;j+=i) {
            vis[j]=1;
        }
    }

    int n;
    int h;
    sa(n);    
    sa(h);
    repp(i,1,n){
        int x;
        sa(x);
        vis[i]=x;
    }
    repp(i,1,h) {
        sa(q[i].le);
        sa(q[i].ri);
        q[i].id=i;
        ans[i]=1;
    }
    rep(i,0,pri.size()) {
        memset(num,0,sizeof(num));
        repp(k,1,n) {
            int g = vis[k];
            while(g%pri[i]==0) {
                num[k]++;
                g/=pri[i];
            }
            vis[k]=g;
        }

        rmq.init(n);
        
        repp(j,1,h) {
            int le = q[j].le;
            int ri = q[j].ri;
            int id = q[j].id;
            ll d = rmq.query_max(le,ri);
            ans[id] = ans[id]*po(pri[i],d,mod)%mod;
        } 
    }
    repp(i,1,n) {
        val[i]=vis[i];
    }
    int bk = ceil(sqrt(1.0*n));
    for (int i = 1; i <= n; i++)
    {
        pos[i] = (i - 1) / bk + 1;
    }
    memset(last,0,sizeof(last));
    repp(i,1,n) {
        v_le[i]=last[val[i]];
        last[val[i]]=i;
    }
    memset(last,0,sizeof(last));
    for(int i=n;i>=1;i--) {
        if(last[val[i]]==0) {
            last[val[i]]=n+1;
        }
        v_ri[i]=last[val[i]];
        last[val[i]]=i;
    }
    int pl = 1; 
    int pr = 0;
    int j;
    val[0]=val[n+1]=0;
    sort(q+1,q+h+1,cmp);
    repp(i,1,n) {
        ni[i] = po(val[i],mod-2,mod);
    }
    ll cur = 1;
    for (int i = 1; i <= h; i++)
    {
        while(pl<q[i].le) {
            if(v_ri[pl]>pr) {
                cur = cur%mod*ni[pl]%mod;
            }
            pl++;
        }
        while(pl>q[i].le) {
            pl--;
            if(v_ri[pl]>pr) {
                cur = cur%mod*val[pl]%mod;
            }
        }
        while(pr>q[i].ri) {
            if(v_le[pr]<pl) {
                cur = cur%mod*ni[pr]%mod;
            }
            pr--;
        }
        while(pr<q[i].ri) {
            pr++;
            if(v_le[pr]<pl) {
                cur = cur%mod*val[pr]%mod;
            }
        }
        ans[q[i].id]=ans[q[i].id]*cur%mod;
    }

    repp(i,1,h){
        ll x;
        printf("%lld\n",ans[i]);
    }

}

```

### [51nod 1695](http://www.51nod.com/Challenge/Problem.html#!#problemId=1695)

好久以前dreamoon的题了。

题意是给定一个树，树上的每个边都有对应的权值。每个点有一个到其他点的最大值$R_x$，问给定一个len，要求找一个点集，这里面$R_x$之间的最大差值小于len，求这个点集的最大值。

完全不会，想了很久，完全不会。。。

首先，考虑求每个点的$R_x$，就是每个点到树的直径的两个点的最大值。这个可求没问题，在会的范围内。。。

怎么求这个最大点集，就不会了。。。。。。。。。。

原来是求树的root，记为r，发现$R_x - R_r < len$ 的都OK，我一想也是哈。。。

之后是树上查分的做法，第一次接触，想了很久，很巧妙。

从该节点往上走到不能抵达的那个节点-1，自己这里+1。每次加上自己子树范围内符合条件的点。

哇，这题出的好赞啊。。。。

```c++
int n;
ll dis[maxn], v[maxn];
vector< pair<int, int> > edge[maxn];

void dfs1(int x, int fa, int d)
{
    if(fa==-1){
        dis[x]=0;
    } else {
        dis[x] = dis[fa] + d;
    }
    for(int i=0;i<edge[x].size();i++) {
        int nxt = edge[x][i].first;
        if(nxt == fa) {
            continue;
        }
        dfs1(nxt,x,edge[x][i].second);
    }
}
int root=1;
ll v2[maxn][2];
void findroot()
{
    memset(dis,0,sizeof(dis));
    dfs1(1, -1, 0);
    int x = 1;
    repp(i,1,n) {
        if(dis[i] > dis[x]) {
            x=i;
        }
    }

    memset(dis,0,sizeof(dis));
    dfs1(x,-1,0);
    int y=1;
    repp(i,1,n) {
        if(dis[i]>dis[y]){
            y=i;
        }
        v2[i][0]=dis[i];
    }

    memset(dis,0,sizeof(dis));
    dfs1(y,-1,0);
    repp(i,1,n) {
        v2[i][1]=dis[i];
    }

    repp(i,1,n) {
        v[i] = max(v2[i][0],v2[i][1]);
    }
    root=1;
    repp(i,1,n) {
        if(v[i]<v[root]) {
            root=i;
        }
    }
    repp(i,1,n) {
        dis[i]=max(v2[i][0],v2[i][1]);
    }
}

int cnt = 0;
int le[maxn],ri[maxn],sum[maxn];
int anc[maxn][20];

void dfsroot(int x,int fa) {
    ++cnt;
    le[x] = cnt;
    anc[x][0] = fa;
    repp(i,1,18){
        if(anc[x][i-1]==-1) {
            anc[x][i]=-1;
            continue;
        }
        anc[x][i] = anc[anc[x][i-1]][i-1];
    }
    for(int i=0;i<edge[x].size();i++) {
        int nxt = edge[x][i].first;
        if(nxt==fa) {
            continue;
        }
        dfsroot(nxt,x);
    }
    ri[x] = cnt;
}

void solve()
{
    sa(n);
    repp(i, 1, n - 1)
    {
        int x, y, z;
        sa(x), sa(y), sa(z);
        edge[x].push_back(mp(y, z));
        edge[y].push_back(mp(x, z));
    }
    findroot();

    dfsroot(root,-1);
    int q;
    sa(q);
    repp(i,1,q) {
        ll len;
        slla(len);

        memset(sum,0,sizeof(sum));
        repp(i,1,n){
            if(dis[i]-dis[root]<=len) {
                sum[le[i]]++;
                continue;
            }
            int y=i;
            for(int k=18;k>=0;k--) {
                if(y==-1) {
                    break;
                }
                if(dis[i]-dis[anc[y][k]]<=len) {
                    y=anc[y][k];
                }
            }
            if(y!=-1)
                y=anc[y][0];
            if(y!=-1)
                sum[le[y]]--;
            sum[le[i]]++;
        }
        repp(i,1,n){
            sum[i] += sum[i-1];
        }
        int ans = 0;
        repp(i,1,n){
            ans = max(ans, sum[ri[i]] - sum[le[i]-1]);
        }
        printf("%d\n", ans);
    }
}

```