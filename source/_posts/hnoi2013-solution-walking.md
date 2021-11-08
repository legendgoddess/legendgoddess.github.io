---
title: "[HNOI2013]游走"
date: 2021-10-07 15:57:02
tags:
  - 期望
  - Dp
categories:
  - 省选题解
---

<h3><center>[HNOI2013]游走</center></h3>

> [[HNOI2013]游走](https://www.luogu.com.cn/problem/P3232)

考虑每一条边的单独贡献，发现这个不是很好计算，而且边数其实很多。

我们考虑对于一条 $u \to v$ 的边，其贡献是 $\dfrac{f(u)}{deg(u)} + \dfrac{f(v)}{deg(v)}$。

其中 $f(i)$ 表示点 $i$ 期望被经过的次数，别忘了一开始点 $1$ 就被经过一次了。

考虑走到别的点之后再走回来更新这个点：
$$
f(u) = \sum_{v \in son(u)} \frac{f(v)}{deg(v)}
$$
如果 $u = 1$ 还要 $+ 1$。

发现数据范围很小，我们直接进行高斯消元即可。

之后对于边的期望经过次数进行贪心即可。

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
const int maxn = 5e2 + 5;
const int maxm = maxn << 1;
const double eps = 1e-10;

int n, m;
double a[maxn][maxn];
double deg[maxn];
vector<int> vc[maxn];

void add(int u,int v) {
    vc[u].push_back(v);
}

double f[maxn];

void Guess() {
    for(int i = 1; i < n; ++ i) {
        int pos(i);
        for(int j = i + 1; j <= n; ++ j) if(fabs(a[j][i]) > fabs(a[pos][i])) pos = i;
        if(pos != i) for(int j = 1; j <= n + 1; ++ j) swap(a[pos][j], a[i][j]);
        for(int j = 1; j <= n; ++ j) if(fabs(a[j][i]) > eps && i != j) {
            double tmp = a[j][i] / a[i][i];
            for(int k = 1; k <= n + 1; ++ k)
                a[j][k] -= tmp * a[i][k];
        }
    }
    for(int i = 1; i < n; ++ i) f[i] = a[i][n + 1] / a[i][i];
    f[n] = 0;
//    for(int i = 1; i <= n; ++ i) printf("%d : %.3lf\n", i, f[i]);
}

struct Edge {
    int u, v;
    double w;
    int operator < (const Edge &z) const {
        return w > z.w;
    }
}E[maxn * maxn];

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n, m);
    for(i = 1; i <= m; ++ i) {int u, v; r1(u, v), add(u, v), add(v, u); E[i].u = u, E[i].v = v;}
    for(i = 1; i <= n; ++ i) deg[i] = vc[i].size();
    for(i = 1; i < n; ++ i) {
        for(int v : vc[i]) if(v != n) {
            a[i][v] = 1.0 / deg[v];
        }
        a[i][i] = -1;
    }
    a[1][n + 1] = -1;

    Guess();

    for(i = 1; i <= m; ++ i) {
        E[i].w = (f[E[i].u] / deg[E[i].u] + f[E[i].v] / deg[E[i].v]);
    }

    sort(E + 1, E + m + 1);
    double ans(0);
    for(i = 1; i <= m; ++ i)
        ans += i * E[i].w;
    printf("%.3lf\n", ans);
	return 0;
}

```




