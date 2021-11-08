---
title: [NOI2018] 归程 题解
date: 2021-10-09 19:47:11
tags:
  - 数据结构
  - 最小生成树
categories:
  - NOI题解
---

<h3><center>[NOI2018] 归程</center></h3>

> [[NOI2018] 归程](https://www.luogu.com.cn/problem/P4768)

讲一下科技 $\tt Kruscal$ 重构树的做法。

考虑到积水的问题，我们肯定是贪心选择积水深度最大的边进行建立重构树。那么这个就是一个小根堆。

之后考虑我们如何回答询问，对于一个询问的水深度，跳父亲。同时维护一下每个点的子树距离 $1$ 节点距离最小的点即可。

---

对于 $\tt Kruscal$ 重构树，每次根据 $\tt Kruscal$ 的过程选出边，但是将边权变成点权之后链接原来边的两个点即可。

```cpp
#include <bits/stdc++.h>
using namespace std;

#define Fread
// #define Getmod

#ifdef Fread
char buf[1 << 21], *iS, *iT;
#define gc() (iS == iT ? (iT = (iS = buf) + fread (buf, 1, 1 << 21, stdin), (iS == iT ? EOF : *iS ++)) : *iS ++)
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

// #define int long long
const int maxn = 8e5 + 5;
const int maxm = maxn << 1;
const int N = 23;

int n, m;

int head[maxn], f[maxn][25];
int fa[maxn], cnt(0);
struct Edge {
    int to, next, w;
}edg[maxn << 1];
void add(int u,int v,int w) {
    edg[++ cnt] = (Edge) {v, head[u], w}, head[u] = cnt;
}

priority_queue<pair<int, int> > q;
int d[maxn], vis[maxn];
void DJ() {
    for(int i = 1; i <= n; ++ i) vis[i] = 0, d[i] = 0x3f3f3f3f;
    d[1] = 0; q.push(make_pair(0, 1));
    while(!q.empty()) {
        int u = q.top().second; q.pop();
        if(vis[u]) continue;
        vis[u] = 1;
        for(int i = head[u];i;i = edg[i].next) {
            int to = edg[i].to;
            if(d[to] > d[u] + edg[i].w) {
                d[to] = d[u] + edg[i].w;
                if(!vis[to]) q.push(make_pair(-d[to], to));
            }
        }
    }
}

struct Edgee {
    int u, v, a;
    int operator < (const Edgee &z) const {
        return a > z.a;
    }
}E[maxn];
struct Node {
    int l, a;
}t[maxn << 1];

int Q, K, S;
int lastans(0);
int getv() {
    int v; r1(v);
    return (v + K * lastans - 1) % n + 1;
}
int getp() {
    int p; r1(p);
    return (p + K * lastans) % (S + 1);
}

vector<int> vc[maxn];

int getfa(int x) {
    return x == fa[x] ? x : fa[x] = getfa(fa[x]);
}
int dep[maxn];
void dfs(int p,int pre) {
    f[p][0] = pre; dep[p] = dep[pre] + 1;
    for(int i = 1; i <= N; ++ i) f[p][i] = f[f[p][i - 1]][i - 1];
    for(vector<int> :: iterator it = vc[p].begin(); it != vc[p].end(); ++ it) {
        int to = *it;
        dfs(to, p);
        t[p].l = min(t[p].l, t[to].l);
    }
}

int query(int v,int p) {
    for(int i = N; ~ i; -- i) {
        // printf("%d %d\n", dep[v]);
        if(dep[v] - (1 << i) > 0) {
    // puts("CCC");
            if(t[f[v][i]].a > p) v = f[v][i];
        }
        // printf("i = %d\n", i);
    }
    return t[v].l;
}

void Kruscal() {
    int i;
    sort(E + 1, E + m + 1);
    for(i = 1; i <= (n << 1); ++ i) fa[i] = i;
    int tot(0);
    int ct(n);
    for(i = 1; i <= m; ++ i) {
        int u = E[i].u, v = E[i].v;
        int u1 = getfa(u), v1 = getfa(v);
        if(u1 == v1) continue;
        ++ ct;
        vc[ct].push_back(u1);
        vc[ct].push_back(v1);
        fa[u1] = fa[v1] = ct;
        t[ct].a = E[i].a;
        ++ tot;
        if(tot == n - 1) break;
    }
    dfs(ct, 0);
}

signed main() {
//    freopen("return5.in", "r", stdin);
//    freopen("S.out", "w", stdout);
	int i, j, T(1);
    r1(T);
    while(T --) {
        r1(n, m);
        for(i = 1; i <= m; ++ i) {
            int u, v, l, a; r1(u, v, l, a);
            E[i] = (Edgee) {u, v, a};
            add(u, v, l), add(v, u, l);
        }
        lastans = 0;
        DJ();
        // puts("ZZZ");
        for(i = 1; i <= (n << 1); ++ i) t[i].l = 2e9;
        for(i = 1; i <= n; ++ i) t[i].l = d[i];
        r1(Q, K, S);
        Kruscal();
        // puts("DDD");
        for(int _ = 1; _ <= Q; ++ _) {
            int v(getv());
            int p(getp());
            // printf("_ = %d\n", _);
            printf("%d\n", lastans = query(v, p));
        }

        cnt = 0;
        for(i = 1; i <= (n << 1); ++ i) fa[i] = head[i] = 0, vc[i].clear(), t[i].a = 0;
        for(i = 1; i <= (n << 1); ++ i) {
            for(j = 0; j <= N; ++ j) f[i][j] = 0;
        }
    }
	return 0;
}

```






