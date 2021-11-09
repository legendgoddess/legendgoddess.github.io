---
title: "CF757G Can Bash Save the Day?"
date: 2021-08-31 15:47:15
tags:
  - 数据结构
categories:
  - CF题解
---

### [CF757G Can Bash Save the Day?](https://codeforces.com/problemset/problem/757/G)

> 时间复杂度分析好题（大雾）。

将询问拆成几部分，也就是 $dis(u, v) = dep(u) + dep(v) - 2 * dep(Lca(u, v))$。

显然对于 $Lca$ 我们直接进行树链剖分即可。

我们对于每一个 $v$ 维护 $\le l$ 的 $dep(Lca)$ 的贡献，为了节省空间我们直接使用可持久化线段树，进行插入的时候使用标记永久化。

具体来说就是将 $[l, r]$ 这个 $dfs$ 序的区间进行 $+ 1$ 意味着，整个区间加上了 $sumdep_r - sumdep_{l - 1}$ 的贡献，之后为了保证线段树的复杂度是 $\log n$ 我们直接在这里记录标记了几次。然后在进行查询的时候直接下传还有多少次标记没有传完即可。直接像普通线段树下传标记会出现下传了不是属于当前线段树节点的情况。

对于修改的话，发现前缀和只有一个位置变了，也就是只需要修改一个位置。

然后因为本题是卡空间的，我们考虑一次加入点是 $\log n$ 的，一次插入也是 $\log n$ 的，本质就是一次插入需要 $\log^2n$ 的空间。根据我们总的线段树空间就可以算出来可以进行直接插入多少次是不会爆空间的。

不然我们就直接重构即可。

**注意：**同样的大小，开结构体占用的空间远大于分开开数组的空间。但是结构体因为是一起开的，所以连续内存访问优化，在时间上会有略微的优势。需要斟酌利弊。

```cpp
#include <bits/stdc++.h>
using namespace std;

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
typedef long long ll;
bool bg;
const int mod = (1 << 30);

int P[maxn], n, Q;
int head[maxn], fa[maxn], dfn[maxn], top[maxn];
struct Edge {
    int to, next, w;
}edg[maxn << 1];
int cnt(1);
void add(int u,int v,int w) {
    edg[++ cnt] = (Edge) {v, head[u], w}, head[u] = cnt;
}
int son[maxn], siz[maxn], fdfn[maxn], dfntot(0);
ll pre[maxn], sum[maxn], dis[maxn], Edis[maxn];
void dfs(int p,int pre) {
    fa[p] = pre, siz[p] = 1;
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to; if(to == pre) continue;
        dis[to] = edg[i].w + dis[p];
        Edis[to] = edg[i].w;
        dfs(to, p);
        siz[p] += siz[to];
        if(siz[son[p]] < siz[to]) son[p] = to;
    }
}

void dfs1(int p,int pre,int topf) {
    top[p] = topf, dfn[p] = ++ dfntot, fdfn[dfntot] = p;
    if(!son[p]) return ;
    dfs1(son[p], p, topf);
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to; if(to == son[p] || to == pre) continue;
        dfs1(to, p, to);
    }
}

int tot(0), Lim;
const int maxT = 37e6;

int tl[maxT], tr[maxT], tag[maxT];

ll val[maxT];
#define ls tl[p]
#define rs tr[p]
#define mid ((l + r) >> 1)
void Insert(int &p,int l,int r,int ll,int rr) {
    if(p <= Lim) {
        ++ tot;
        tl[tot] = tl[p], tr[tot] = tr[p], tag[tot] = tag[p], val[tot] = val[p];
        p = tot;
    }
    if(ll <= l && r <= rr) return ++ tag[p], val[p] += pre[r] - pre[l - 1], void();
    if(ll <= mid) Insert(ls, l, mid, ll, rr);
    if(mid < rr) Insert(rs, mid + 1, r, ll, rr);
    val[p] = val[ls] + val[rs] + (pre[r] - pre[l - 1]) * tag[p];
}
ll Ask(int p,int l,int r,int ll,int rr,int times) {
    if(!p) return times * (pre[min(rr, r)] - pre[max(l - 1, ll - 1)]);
    if(ll <= l && r <= rr) return times * (pre[r] - pre[l - 1]) + val[p];
    times += tag[p];
    long long res(0);
    if(ll <= mid) res += Ask(ls, l, mid, ll, rr, times);
    if(mid < rr) res += Ask(rs, mid + 1, r, ll, rr, times);
    return res;
}
#undef ls
#undef rs
#undef mid

int rt[maxn];

void Insert(int l,int v) {
    Lim = tot;
    while(v) {
        Insert(rt[l], 1, n, dfn[top[v]], dfn[v]);
        v = fa[top[v]];
    }
}

ll Ask(int l,int v) {
    ll ans = sum[l] + dis[v] * l;
    while(v) {
        ans -= Ask(rt[l], 1, n, dfn[top[v]], dfn[v], 0) * 2;
        v = fa[top[v]];
    }
    return ans;
}

bool ed;

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
//    cerr << (&ed - &bg) / 1024.0 / 1024.0 << endl;
    ll lastans(0);
    int i, j;
    r1(n, Q);
    for(i = 1; i <= n; ++ i) r1(P[i]);
    for(i = 1; i < n; ++ i) {
        int u, v, w;
        r1(u, v, w), add(u, v, w), add(v, u, w);
    }
    dfs(1, 0);
    dfs1(1, 0, 1);

    for(i = 1; i <= n; ++ i) pre[i] = pre[i - 1] + Edis[fdfn[i]];
    for(i = 1; i <= n; ++ i) sum[i] = sum[i - 1] + dis[P[i]];
    for(i = 1; i <= n; ++ i) rt[i] = rt[i - 1], Insert(i, P[i]);

    int Ts(0);

    while(Q --) {
        int opt, l, r, x;
        r1(opt);
        if(opt == 1) {
            r1(l, r, x);
            l = lastans ^ l;
            r = lastans ^ r;
            x = lastans ^ x;
//            printf("l = %d, r = %d, x = %d\n", l, r, x);
            lastans = Ask(r, x) - Ask(l - 1, x);
            printf("%lld\n", lastans);
            lastans %= mod;
        }
        else {
            r1(x);
            x = lastans ^ x;
            swap(P[x], P[x + 1]), sum[x] = sum[x - 1] + dis[P[x]];
            if(++ Ts == 150000) { // 重构
                Ts = 0; tot = 0;
                for(i = 1; i <= n; ++ i) rt[i] = rt[i - 1], Insert(i, P[i]);
            }
            else rt[x] = rt[x - 1], Insert(x, P[x]); // 重新插入，废弃之前
        }
    }
	return 0;
}
```






