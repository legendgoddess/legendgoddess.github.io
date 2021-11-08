---
title: P3412 仓鼠找sugar II
date: 2021-10-07 18:59:28
tags:
  - Dp
  - 期望
categories:
  - 洛谷题解
---

<h3><center>P3412 仓鼠找sugar II</center></h3>

> [P3412 仓鼠找sugar II](https://www.luogu.com.cn/problem/P3412)

根据期望的线性性质我们考虑对于每一条边进行计算贡献。

我们可以考虑先算方案数再除以总的点对。

根据期望的定义本质上就是平均数，那么对于一条边 $u \to v$ 的贡献次数就是 $(n - siz(u)) \times siz(u)$。

我们考虑一条边有两种情况：

- 向上
- 向下

我们钦定点 $u$ 表示 $fa(u) \to u$ 的边。

- 设 $up(u)$ 表示这条边从下走到上 $u \to fa(u)$。
- 设 $dn(u)$ 表示这条边从下走到上 $fa(u) \to u$。

$up(u)$ 一共有两种情况：

- $u \to v \to u \to fa(u)$
- $u \to fa(u)$。

$$
\begin{aligned}
up( u) &= \frac{1}{deg(u)} + \sum_{v \in son(u)} \frac{up(v) + up(u) + 1}{deg(u)}\\
&= deg(u) + \sum_{v \in son(u)} up(v)
\end{aligned}
$$

$dn(u)$ 一共有 $3$ 种情况：

- $fa(u) \to u$
- $fa(u) \to u' \to fa(u) \to u$
- $fa(u) \to fa(fa(u)) \to u$

设 $sum(u) = \sum_{v \in son(u)} up(v)$。
$$
\begin{aligned}
dn(u) &= \frac{1}{deg(fa(u))} + \sum_{v \in son(fa(u)), v \ne u} \frac{up(v) + dn(u) + 1}{deg(fa(u))} + \frac{dn(fa(u)) + dn(u) + 1}{deg(fa(u))} \\
&= deg(u) + sum(fa(u)) - up(u) + dn(fa(u))
\end{aligned}
$$

之后容易想到一条边的贡献就是向上和向下之和 $ans = siz(u) \times (n - siz(u)) \times (up(u) + dn(u))$。

之后再 $\div n^2$ 即可。

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
const int mod = 998244353;

int head[maxn], cnt;
struct Edge {
    int to, next;
}edg[maxn << 1];
void add(int u,int v) {
    edg[++ cnt] = (Edge) {v, head[u]}, head[u] = cnt;
}

int n;
int up[maxn], dn[maxn], sum[maxn], deg[maxn], siz[maxn];

void dfs1(int p,int pre) {
    siz[p] = 1;
    for(int i = head[p];i;i = edg[i].next) {
        int v = edg[i].to;
        ++ deg[p];
        if(v == pre) continue;
        dfs1(v, p);
        sum[p] = (sum[p] + up[v]) % mod;
        siz[p] += siz[v];
    }
    up[p] = (deg[p] + sum[p]) % mod;
}

int ksm(int x,int mi) {
    int res(1);
    while(mi) {
        if(mi & 1) res = 1ll * res * x % mod;
        mi >>= 1;
        x = 1ll * x * x % mod;
    }
    return res;
}
int ans(0);
void dfs2(int p,int pre) {
    if(p != 1) dn[p] = (deg[pre] + sum[pre] - up[p] + dn[pre] + mod) % mod;
    ans += 1ll * siz[p] * (n - siz[p]) % mod * (up[p] + dn[p]) % mod;
    ans %= mod;
    for(int i = head[p];i;i = edg[i].next){
        int to = edg[i].to; if(to == pre) continue;
        dfs2(to, p);
    }
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n);
    for(i = 1; i < n; ++ i) {
        int u, v;
        r1(u, v), add(u, v), add(v, u);
    }
    dfs1(1, 0);
    dfs2(1, 0);
//    printf("ans = %d\n", ans);
    ans = 1ll * ans * ksm(1ll * n * n % mod, mod - 2) % mod;
    printf("%d\n", ans);
	return 0;
}
```








