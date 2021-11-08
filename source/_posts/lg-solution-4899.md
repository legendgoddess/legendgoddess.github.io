---
title: "[IOI2018] werewolf 狼人"
date: 2021-10-09 19:38:31
tags:
  - 数据结构
  - 最小生成树
categories:
  - IOI题解
---

<h3><center>[IOI2018] werewolf 狼人</center></h3>

> [[IOI2018] werewolf 狼人](https://www.luogu.com.cn/problem/P4899)

可以考虑到每一条边能否通过对于人形态来说，取决于两点的最小值，对于狼形态来说取决两点的最大值。

那么我们不妨考虑将两条边的边权定为上述两者，这样就有两张联通的无向图，我们考虑既然不考虑路程的限制我们一个肯定是需要考虑最大生成树另外一个就是最小生成树。

使用 $\tt Kruscal$ 重构树建立两棵树，一个是大根堆另一个是小根堆。

对于两个 $x, y$ 能否到达取决于是否存在一个点同时是两者的儿子。

我们考虑维护 $\tt dfs$ 序，通过可持久化线段树来判断。

但是题目中还有 $L, R$ 的限制，我们考虑到对于一条路径我们其实对于树上一个点还能向上走，我们考虑倍增跳到一个最高的合法的位置进行上述判断即可。

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

int fa[maxn];
int getfa(int x) {
    return x == fa[x] ? x : fa[x] = getfa(fa[x]);
}

int head[maxn], cnt;
struct Edge {
    int to, next;
}edg[maxn << 2];
void add(int u,int v) {
    edg[++ cnt] = (Edge) {v, head[u]}, head[u] = cnt;
}

vector<int> vc1[maxn], vc2[maxn];

void add(vector<int> *vc,int u,int v) {
    vc[u].push_back(v);
}

void merge(int u,int v) {
    u = getfa(u), v = getfa(v);
    if(u != v)
    fa[u] = v;
}

int n, m, Q;

int d1l[maxn], d1r[maxn], d2l[maxn], d2r[maxn];
int fid[maxn];
int tot1(0), tot2(0);
const int N = 20;
int fa1[maxn][N + 5], fa2[maxn][N + 5];

void dfs1(int p,int pre) {
    d1l[p] = ++ tot1;
    fid[tot1] = p;
    fa1[p][0] = pre;
    for(int i = 1; i <= N; ++ i) fa1[p][i] = fa1[fa1[p][i - 1]][i - 1];
    for(int v : vc1[p]) if(v != pre) dfs1(v, p);
    d1r[p] = tot1;
}

void dfs2(int p,int pre) {
    d2l[p] = ++ tot2;
    fa2[p][0] = pre;
    for(int i = 1; i <= N; ++ i) fa2[p][i] = fa2[fa2[p][i - 1]][i - 1];
    for(int v : vc2[p]) if(v != pre) dfs2(v, p);
    d2r[p] = tot2;
}

int rt[maxn];
int lsn[maxn * 25], rsn[maxn * 25], t[maxn * 25];

struct Seg {
    #define ls lsn[p]
    #define rs rsn[p]
    #define mid ((l + r) >> 1)
    int tot;
    Seg(void) { tot = 0; }
    void Insert(int &p,int pre,int l,int r,int pos) {
        p = ++ tot; t[p] = t[pre], ls = lsn[pre], rs = rsn[pre];
        ++ t[p];
        if(l == r) return ;
        if(pos <= mid) Insert(ls, lsn[pre], l, mid, pos);
        else Insert(rs, rsn[pre], mid + 1, r, pos);
    }
    int ask(int p,int l,int r,int ll,int rr) {
        if(!p) return 0;
        if(ll <= l && r <= rr) return t[p];
        int res(0);
        if(ll <= mid) res += ask(ls, l, mid, ll, rr);
        if(mid < rr) res += ask(rs, mid + 1, r, ll, rr);
        return res;
    }
}T;

int findpre1(int x,int v) {
    for(int i = N; i >= 0; -- i) if(fa1[x][i] >= v && fa1[x][i] != 0) x = fa1[x][i];
    return x;
}

int findpre2(int x,int v) {
    for(int i = N; i >= 0; -- i) if(fa2[x][i] <= v && fa2[x][i] != 0) x = fa2[x][i];
    return x;
}

signed main() {
//    freopen("S.in", "r", stdin);
    int i, j;
    r1(n, m, Q);
//    puts("ZZZZ");
    for(i = 1; i <= m; ++ i) {
        int u, v; r1(u, v);
        ++ u, ++ v;
        add(u, v), add(v, u);
    }
    for(i = 1; i <= n; ++ i) fa[i] = i;
    for(i = n; i >= 1; -- i) {
        for(int j = head[i];j;j = edg[j].next) {
            int to = edg[j].to; if(getfa(to) > i) {
                add(vc1, i, getfa(to));
                merge(to, i);
            }
        }
    }
    for(i = 1; i <= n; ++ i) fa[i] = i;
    for(i = 1; i <= n; ++ i) {
        for(int j = head[i];j;j = edg[j].next) {
            int to = edg[j].to; if(getfa(to) < i) {
                add(vc2, i, getfa(to));
                merge(to, i);
            }
        }
    }
    dfs1(1, 0);
    dfs2(n, 0);
    for(i = 1; i <= n; ++ i)
        T.Insert(rt[i], rt[i - 1], 1, n, d2l[fid[i]]);
    while(Q --) {
        int x, y, l, r;
        r1(x, y, l, r);
        ++ x, ++ y, ++ l, ++ r;
        int nx = findpre1(x, l), ny = findpre2(y, r);
//        printf("%d %d\n", nx, ny);
        int ans = T.ask(rt[d1r[nx]], 1, n, d2l[ny], d2r[ny]) - T.ask(rt[d1l[nx] - 1], 1, n, d2l[ny], d2r[ny]);
//        printf("ans = %d\n", ans);
        if(ans > 0) puts("1");
        else puts("0");
    }
    return 0;
}

/*
6 6 3
5 1
1 2
1 3
3 4
3 0
5 2
4 2 1 2
4 2 2 2
5 4 3 4
*/

```




