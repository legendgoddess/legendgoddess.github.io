---
title: CF980F Cactus to Tree
date: 2021-08-15 16:03:42
tags:
  - Dp
  - 单调队列
categories:
  - CF题解
---

### [CF980F](https://www.luogu.com.cn/problem/CF980F)

每一个点都指属于一个环，考虑对于每一个环进行计算答案。

考虑基环树的情况，我们会计算出其余的点，之后对于环上的点单独进行 $Dp$。

这边也是同理，找到每一个点属于的环，钦定一下每一个环的根。

因为计算距离需要使用换根 $Dp$。

我们考虑需要预处理写什么，也就是对于不同环之间的转移，然后对于同一个环内我们需要记录环根的答案。

对于不同环之间的转移，对于每一个点设 $f0_i$ 表示向下，从所属环不同的点到当前环的最小距离。

然后对于环根还要特别记录一下 $f1_i$ 表示考虑环上的情况。

> 因为环根才可以将之前的节点都考虑到。

之后设 $f_i = \max(f0_i, f1_i)$ 也就是向下走的答案。

之后考虑向上走，设 $g_i$ 表示向上走的答案。

我们这个时候可以将 $f0_i = \max(f0_i, g_i)$ 方便处理。

可以从所属环不同的点进行转移，记录最大最次大值。

之后考虑整个环对于当前点进行转移，也就是 $\min(l, r)$ 左右两边，我们分别对其进行单调队列即可。

------

可能因为我代码实现问题，我的数组需要多开大几倍。

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

#ifdef Getmod
const int mod  = 1e9 + 7;
template <int mod>
struct typemod {
    int z;
    typemod(int a = 0) : z(a) {}
    inline int inc(int a,int b) const {return a += b - mod, a + ((a >> 31) & mod);}
    inline int dec(int a,int b) const {return a -= b, a + ((a >> 31) & mod);}
    inline int mul(int a,int b) const {return 1ll * a * b % mod;}
    typemod<mod> operator + (const typemod<mod> &x) const {return typemod(inc(z, x.z));}
    typemod<mod> operator - (const typemod<mod> &x) const {return typemod(dec(z, x.z));}
    typemod<mod> operator * (const typemod<mod> &x) const {return typemod(mul(z, x.z));}
    typemod<mod>& operator += (const typemod<mod> &x) {*this = *this + x; return *this;}
    typemod<mod>& operator -= (const typemod<mod> &x) {*this = *this - x; return *this;}
    typemod<mod>& operator *= (const typemod<mod> &x) {*this = *this * x; return *this;}
    int operator == (const typemod<mod> &x) const {return x.z == z;}
    int operator != (const typemod<mod> &x) const {return x.z != z;}
};
typedef typemod<mod> Tm;
#endif

template <typename T,typename... Args> inline void r1(T& t, Args&... args) {
    r1(t);  r1(args...);
}

//#define int long long
const int maxn = 2e6 + 5;
const int maxm = maxn << 1;

vector<int> vc[maxn];
void add(int u,int v) {
    vc[u].push_back(v);
}
int f[maxn], g[maxn], f0[maxn], f1[maxn];
int mx[maxn][2];
vector<int> cir[maxn];
int bel[maxn];
int n, m;


int vis[maxn], fa[maxn];

void dfs(int p,int pre) {
    vis[p] = 1, fa[p] = pre;
    for(auto v : vc[p]) {
        if(v == pre) continue;
        if(vis[v]) {
            if(bel[p]) continue;
            ++ m;
            for(int j = p; j != v; j = fa[j])
                bel[j] = m, cir[m].push_back(j);
            bel[v] = m, cir[m].push_back(v);
//            printf("m = %d\n", m);
            continue;
        }
        dfs(v, p);
    }
    if(!bel[p]) {
//            printf("m = %d\n", m);
        cir[++ m].push_back(p);
        bel[p] = m;
    }
}

void dpf(int p) {
    vis[p] = 1;
    for(auto v : vc[p]) {
        if(vis[v]) continue;
        dpf(v);
        if(bel[v] != bel[p]) {
            f0[p] = max(f0[p], f[v] + 1);
            if(!mx[p][0] || f[mx[p][0]] < f[v]) mx[p][1] = mx[p][0], mx[p][0] = v;
            else if(!mx[p][1] || f[mx[p][1]] < f[v]) mx[p][1] = v;
        }
    }
    int x = bel[p], sz = cir[x].size();
    if(p == cir[x][sz - 1]) {
        for(int i = 0; i < sz; ++ i) {
            int v = cir[x][i];
            f1[p] = max(f1[p], min(sz - i - 1, i + 1) + f[v]);
        }
    }// 这个是考虑向下的答案，每一个环的最后一个节点是有用的，之后其余的节点需要进行换根处理
    f[p] = max(f0[p], f1[p]);
}

struct Node {
    int val, id;
    Node(int a = 0,int b = 0) : val(a), id(b) {}
    int operator < (const Node &z) const {
        return val < z.val;
    }
    int operator <= (const Node &z) const {
        return val <= z.val;
    }
}q[maxn];

int hd, tl;

void dpg(int p) {
    vis[p] = 1; int pre = fa[p];
    int x = bel[p], sz = cir[x].size();
    if(pre && bel[pre] != bel[p]) {
        g[p] = max(g[p], g[pre] + 1);
        g[p] = max(g[p], f1[pre] + 1);
        if(mx[pre][0] == p) {
            if(mx[pre][1]) g[p] = max(g[p], f[mx[pre][1]] + 2);
        }
        else g[p] = max(g[p], f0[pre] + 1);
    }
    f0[p] = max(f0[p], g[p]);
    if(p == cir[x][sz - 1]) {
        hd = tl = 1;
        q[1] = Node(f0[cir[x][0]], 0);
        for(int i = 1; i <= sz / 2; ++ i) {
            while(hd <= tl && q[tl] <= f0[cir[x][i]] + i) -- tl;
            q[++ tl] = Node (f0[cir[x][i]] + i, i);
        }
        auto Max = [&] (int &s, const int &c) {
            return (s < c ? s = c : 0), void();
        };
        for(int i = 0; i < sz - 1; ++ i) { // 环根已经被计算过了
            if(q[hd].id == i) ++ hd;
            Max(g[cir[x][i]], q[hd].val - i);
            int nx = i + sz / 2 + 1;
            while(hd <= tl && q[tl] <= f0[cir[x][nx % sz]] + nx) -- tl;
            q[++ tl] = Node (f0[cir[x][nx % sz]] + nx, nx);
        }

        hd = tl = 1;
        q[1] = Node (f0[cir[x][sz / 2]] + sz / 2, sz / 2);
        for(int i = sz / 2 + 1; i < sz; ++ i) {
            while(hd <= tl && q[tl] <= f0[cir[x][i]] + sz - i) -- tl;
            q[++ tl] = Node (f0[cir[x][i]] + sz - i, i);
        }
        for(int i = 0; i < sz - 1; ++ i) {
            if(q[hd].id == sz / 2 + i) ++ hd;
            Max(g[cir[x][i]], q[hd].val + i);
            int nx = sz + i;
            while(hd <= tl && q[tl] <= f0[cir[x][nx % sz]] + sz - nx) -- tl;
            q[++ tl] = Node (f0[cir[x][nx % sz]] + sz - nx, nx);
        }
    }
    for(auto v : vc[p]) if(!vis[v]) dpg(v);
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n, m);
    for(i = 1; i <= m; ++ i) {
        int u, v;
        r1(u, v);
        add(u, v), add(v, u);
    }
    m = 0;
    dfs(1, 0);
    memset(vis, 0, sizeof(vis));
    dpf(1);
    memset(vis, 0, sizeof(vis));
    dpg(1);
    for(i = 1; i <= n; ++ i) printf("%d%c", max(f[i], g[i]), " \n"[i == n]);
	return 0;
}
```




