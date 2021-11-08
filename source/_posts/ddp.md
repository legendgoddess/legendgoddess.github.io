---
title: P4719 【模板】“动态 DP“&动态树分治 题解
date: 2021-10-08 16:26:05
tags:
  - Dp
  - 数据结构
categories:
  - 算法
---

<h3><center>P4719 【模板】"动态 DP"&动态树分治</center></h3>

> [P4719 【模板】"动态 DP"&动态树分治](https://www.luogu.com.cn/problem/P4719)

先不考虑修改，可以想到 $\tt Dp$：

设 $f(i, 0/1)$ 表示当前的点是否选择，那么转移可以得到：
$$
\begin{aligned}
f(u, 0) &= \sum_{v \in son_u} \max(f(v, 0), f(v, 1))\\
f(u, 1) &= \sum_{v \in son_u} f(v, 0)
\end{aligned}
$$
发现每一次进行修改的时候影响的是一条链上的信息，我们不妨将其树链剖分一下，设 $g(u, 0/1)$ 表示亲儿子对应的信息。
$$
\begin{aligned}
g(u, 0) =& \sum_{v \in sonlight_u} \max(f(v, 0), f(v, 1)) \\
g(u, 1) =& \sum_{v \in sonlight_u} f(v, 0)
\end{aligned}
$$
我们可以把转移变成矩阵：
$$
\left[
\begin{matrix}
f(v, 0) & f(v, 1)
\end{matrix}
\right]
\times 
\left[
\begin{matrix}
g(u, 0) & g(u, 1) \\
g(u, 0) & - \infty
\end{matrix}
\right]=
\left[
\begin{matrix}
f(u, 0) & f(u, 1)
\end{matrix}
\right]
$$
我们使用线段树进行维护重儿子，每次跳链。复杂度是 $O(n \log ^2 n)$。

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
const int maxn = 1e5 + 5;
const int maxm = maxn << 1;
const int inf = 1e9;
struct Matrix {
    int a[2][2];
    Matrix(void) { memset(a, 0, sizeof(a)); }
    int * operator [] (const int &x) { return a[x]; }
    void init() {
        memset(a, 0, sizeof(a));
        a[0][1] = a[1][0] = -inf;
    }
    void Min() {
        for(int i = 0; i < 2; ++ i) a[i][0] = a[i][1] = -inf;
    }
    Matrix operator * (const Matrix &z) const {
        Matrix res; res.Min();
        for(int i = 0; i < 2; ++ i) {
            for(int j = 0; j < 2; ++ j)
            for(int k = 0; k < 2; ++ k)
            res.a[i][j] = max(res.a[i][j], a[i][k] + z.a[k][j]);
        }
        return res;
    }
};

vector<int> vc[maxn];
void add(int u,int v) {
    vc[u].emplace_back(v);
}

int dfntot(0);
int dfn[maxn], fdfn[maxn], top[maxn], fa[maxn], son[maxn], siz[maxn];
int bot[maxn];
void dfs(int p,int pre) {
    siz[p] = 1;
    for(int v : vc[p]) if(v != pre) {
        dfs(v, p);
        siz[p] += siz[v];
        if(siz[v] > siz[son[p]]) son[p] = v;
    }
}

void dfs1(int p,int pre,int topf) {
    dfn[p] = ++ dfntot;
    fdfn[dfntot] = p;
    top[p] = topf;
    fa[p] = pre;
    if(son[p]) dfs1(son[p], p, topf), bot[p] = bot[son[p]];
    else bot[p] = p;
    for(int v : vc[p]) if(v != pre && v != son[p]) {
        dfs1(v, p, v);
    }
}
int f[maxn][2], g[maxn][2], a[maxn];
void dfs2(int p,int pre) {
    f[p][1] = a[p];
    if(son[p]) dfs2(son[p], p), f[p][0] = max(f[son[p]][0], f[son[p]][1]), f[p][1] += f[son[p]][0];
    for(int v : vc[p]) if(v != pre && v != son[p]) {
        dfs2(v, p);
        g[p][0] += max(f[v][0], f[v][1]);
        g[p][1] += f[v][0];
    }
    f[p][0] += g[p][0];
    f[p][1] += g[p][1];
}

Matrix t[maxn << 2];
struct Seg {
    #define ls (p << 1)
    #define rs (p << 1 | 1)
    #define mid ((l + r) >> 1)

    void pushup(int p) {
        t[p] = t[rs] * t[ls];
    }

    void build(int p,int l,int r) {
        if(l == r) {
            int id = fdfn[l];
            t[p].a[0][0] = g[id][0];
            t[p].a[1][0] = g[id][0];
            t[p].a[0][1] = g[id][1] + a[id];
            t[p].a[1][1] = - inf;
            return ;
        }
        build(ls, l, mid), build(rs, mid + 1, r);
        pushup(p);
    }

    void change(int p,int l,int r,int pos) {
        if(l == r) {
            int id = fdfn[pos];
            t[p].a[0][0] = g[id][0];
            t[p].a[1][0] = g[id][0];
            t[p].a[0][1] = g[id][1] + a[id];
            t[p].a[1][1] = - inf;
            return ;
        }
        if(pos <= mid) change(ls, l, mid, pos);
        else change(rs, mid + 1, r, pos);
        pushup(p);
    }

    Matrix Ask(int p,int l,int r,int ll,int rr) {
        if(ll <= l && r <= rr) return t[p];
        Matrix res; res.init();
        if(mid < rr) res = res * Ask(rs, mid + 1, r, ll, rr);
        if(ll <= mid) res = res * Ask(ls, l, mid, ll, rr);
        return res;
    }

    #undef ls
    #undef rs
    #undef mid
}T;

int n, m;

void Solve(int u) {
    while(top[u] != 1) {
        Matrix res = T.Ask(1, 1, n, dfn[top[u]], dfn[bot[u]]);
        g[fa[top[u]]][0] -= max(res.a[0][0], res.a[0][1]);
        g[fa[top[u]]][1] -= res.a[0][0];
        T.change(1, 1, n, dfn[u]);
        res = T.Ask(1, 1, n, dfn[top[u]], dfn[bot[u]]);
        g[fa[top[u]]][0] += max(res.a[0][0], res.a[0][1]);
        g[fa[top[u]]][1] += res.a[0][0];
//        printf("%d\n", top[u]);
        u = fa[top[u]];
//        printf("u = %d\n", u);
    }
    T.change(1, 1, n, dfn[u]);
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n, m);
    for(i = 1; i <= n; ++ i) r1(a[i]);
    for(i = 1; i < n; ++ i) {
        int u, v; r1(u, v), add(u, v), add(v, u);
    }
    dfs(1, 0);
    dfs1(1, 0, 1);
    dfs2(1, 0);
//    for(i = 1; i <= n; ++ i) printf("%d : %d\n", i, dfn[i]);
//    for(i = 1; i <= n; ++ i) printf("%d : %d\n", i, fdfn[i]);
    T.build(1, 1, n);
    for(i = 1; i <= m; ++ i) {
        int x, y;
        r1(x, y), a[x] = y;
        Solve(x);
        Matrix res = T.Ask(1, 1, n, dfn[1], dfn[bot[1]]);
        printf("%d\n", max(res.a[0][0], res.a[0][1]));
    }
	return 0;
}
```


