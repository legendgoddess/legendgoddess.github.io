---
title: CF150E Freezing with Style
date: 2021-09-03 11:02:47
tags:
  - Dp
categories:
  - CF题解
---

### [CF150E Freezing with Style](https://codeforces.com/problemset/problem/150/E)

> 经典的更优复杂度不会，大众分都懂。

就是对于中位数的话进行树上路径合并很难处理，那么我们不妨二分中位数，之后将 $\ge$ 的数作为 $1$，否则就是 $-1$。那么合法的一条路径肯定是权值和 $\ge 0$。

> 前面取等号是题目的要求。

那么对于一条路径如果其长度是 $\tt len$ 那么我们合法的配对路径的取值范围就是 $\tt[L - len, R - len]$。

因为要使其合法所以我们只要使用这个区间的最大值就好了。如果我们将路径长度从大到小排序，那么这个区间就是逐渐向右移动，本质上就是一个滑动窗口问题。

那么我们合并就可以处理了，具体来说就是将之前子树的信息记录，之后枚举当前子树的中路径的长度（这里相同长度的路径显然只要考虑权值最大的一个）。每次更新一下滑动窗口的区间即可。

最后我们再将当前子树和之前合并就好了。

------

一些代码中使用的数组：

```Dp[i]```：已经处理过的子树中深度为 $i$ 的最大权值的节点编号。

```Dmx[i]```：已经处理过的子树中深度为 $i$ 的最大权值。

```dp[i]```：正在处理过的子树中深度为 $i$ 的最大权值的节点编号。

```dmx[i]```：正在处理过的子树中深度为 $i$ 的最大权值。

```Mdep```：已经处理过的子树中的最大深度。

```cpp
#include <bits/stdc++.h>
using namespace std;

//#define Fread
//#define Getmod

#ifdef Fread
char buf[1 << 21], *iS, *iT;
#define gc() (iS == iT ? (iT = (iS = buf) + fread (buf, 1, 1 << 21, stdin), (iS == iT ? EOF : *iS ++)) : *iS ++)
#define getchar gc
#endif // Fread

template <typename T>
void r1(T &x) {
	x = 0;
	char c(getchar());
	int f(1);
	for(; c < '0' || c > '9'; c = getchar()) if(c == '-') f = -1;
	for(; '0' <= c && c <= '9';c = getchar()) x = (x * 10) + (c ^ 48);
	x *= f;
}

template <typename T,typename... Args> inline void r1(T& t, Args&... args) {
    r1(t);  r1(args...);
}

//#define int long long
const int maxn = 2e5 + 5;
const int maxm = maxn << 1;

struct Edge {
    int v, w;
};

vector<Edge> vc[maxn];

int siz[maxn], mx[maxn], mxdep[maxn], dep1[maxn], vis[maxn];
int Rt, Mx, All;
void getrt(int p,int pre) {
//    printf("p = %d\n", p);
    siz[p] = 1, mx[p] = 0, mxdep[p] = dep1[p];
    for(auto v : vc[p]) {
        if(v.v == pre || vis[v.v]) continue;
        dep1[v.v] = dep1[p] + 1;
        getrt(v.v, p);
        siz[p] += siz[v.v];
        mx[p] = max(mx[p], siz[v.v]);
        mxdep[p] = max(mxdep[p], mxdep[v.v]);
    }
    mx[p] = max(mx[p], All - siz[p]);
    if(mx[p] < Mx) Rt = p, Mx = mx[p];
}

int L, R, dis[maxn], Dep[maxn], Ansu, Ansv, M;
int buc[maxn], fa[maxn];

int bfs(int st) {
    int tot(0);
    buc[++ tot] = st;
    static queue<int> q; while(!q.empty()) q.pop();
    q.push(st);
    while(!q.empty()) {
        int u = q.front(); q.pop();
        for(auto v : vc[u]) if(v.v != fa[u] && !vis[v.v]) {
            fa[v.v] = u, Dep[v.v] = Dep[u] + 1; dis[v.v] = dis[u] + (v.w >= M ? 1 : -1);
            buc[++ tot] = v.v;
            q.push(v.v);
        }
    }
    return tot;
}

int q[maxn], Dmx[maxn], Dp[maxn], dmx[maxn], dp[maxn], Mdep;
const int inf = 0x3f3f3f3f;

int Calc(int p) {
    int Len = bfs(p);
    for(int i = 1; i <= Len; ++ i) if(dmx[Dep[buc[i]]] < dis[buc[i]]) {
        dmx[Dep[buc[i]]] = dis[buc[i]];
        dp[Dep[buc[i]]] = buc[i];
    }
    int hd(1), tl(0);
    int flag(0);
    for(int i = mxdep[p], j = 0; i; -- i) {
        int ll = L - i, rr = R - i;
        if(ll > Mdep) break;
        j = max(j, ll);
        for(; j <= rr && j <= Mdep; ++ j) {
            for(; hd <= tl && Dmx[q[tl]] <= Dmx[j]; -- tl);
            q[++ tl] = j;
        }
        for(; hd <= tl && q[hd] < ll; ++ hd);
        if(hd <= tl && Dmx[q[hd]] + dmx[i] >= 0) {
            Ansu = dp[i], Ansv = Dp[q[hd]]; // Dp[i] 表示深度为 i 的最大权值的位置
            flag = 1;
            break;
        }
    }

    Mdep = max(Mdep, mxdep[p]);

    for(int i = 1; i <= mxdep[p]; ++ i) if(Dmx[i] < dmx[i]) {
        Dmx[i] = dmx[i]; Dp[i] = dp[i];
        dp[i] = 0, dmx[i] = - inf;
    }

    return flag;
}

int dfs(int p) {
//    printf("p = %d\n", p);
    vis[p] = 1;
    sort(vc[p].begin(), vc[p].end(), [&] (const Edge &a, const Edge &b) {
        return mxdep[a.v] < mxdep[b.v];
    });

    Dp[0] = p, Dmx[0] = 0;
    for(int i = 1; i <= mxdep[p]; ++ i) Dmx[i] = dmx[i] = - inf, Dp[i] = dp[i] = 0;
    Mdep = 0;

    for(auto v : vc[p]) if(!vis[v.v]) {
        fa[v.v] = p;
        dis[v.v] = (v.w >= M ? 1 : -1);
        Dep[v.v] = 1;
        if(Calc(v.v)) return 1;
    }
    for(auto v : vc[p]) if(!vis[v.v]) {
        All = siz[v.v];
        Mx = 2e9, getrt(v.v, p);
        dep1[Rt] = 0;
        getrt(Rt, 0);
        if(dfs(Rt)) return 1;
    }
    return 0;
}
int val[maxn], n;
signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n, L, R);
    for(i = 1; i < n; ++ i) {
        int u, v, w;
        r1(u, v, w);
        auto add = [&] (int u,int v,int w) {
            vc[u].push_back((Edge) {v, w});
        };
        add(u, v, w), add(v, u, w);
        val[i] = w;
    }
    sort(val + 1, val + n);
    int mid, l(1), r(n - 1), ansu, ansv, ansmid;
    while(l <= r) {
        mid = (l + r) >> 1;
        memset(vis, 0, sizeof(vis));
        M = val[mid];
        Mx = 2e9, All = n;
        getrt(1, 0); dep1[Rt] = 0;
//    puts("ZZZ");
        getrt(Rt, 0);
        if(dfs(Rt)) l = mid + 1, ansu = Ansu, ansv = Ansv, ansmid = mid;
        else r = mid - 1;
    }
//    printf("ansmid = %d\n", val[ansmid]);
    printf("%d %d\n", ansu, ansv);
	return 0;
}
```

