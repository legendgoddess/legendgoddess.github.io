---
title: POJ 3613 Cow Relays
date: 2021-09-27 10:55:36
tags:
  - Dp
categories:
  - POJ题解
---

<h3><center>POJ 3613 Cow Relays</center></h3>

> [POJ 3613 Cow Relays](http://poj.org/problem?id=3613)

> 真心想吐槽一下，这个不能用 $11$ 真的挺难受的。

首先看去像一个板子题，之后发现如果直接对于每个点建图的话时间是过不了的。

之后发现边的数量其实不是很多，一条边最多只有两个不同的点，所以实际上有用的点数是 $2m + 2$ 个。我们对于这个直接进行矩阵快速幂即可。

其实还有一个稍微难写一点的方法，我们考虑对于每一条边建立矩阵，之后如果两条边能互相到达就赋值为其权值，具体来说是 $i \to j, j \to z$ 边权分别是 $a_x, a_y$。那么图上 $G(x, y) = a_y, G(y, x) = a_x$。也就是具体表示走到了这条边的尽头的贡献。

那么我们发现 $n \ge 2$ 实际上我们考虑先让 $S$ 走到能走到的边的尽头作为答案矩阵。之后直接矩阵快速幂更新即可。

> 这个是我一开始想到的，后面发现其实离散化一下就可以了，所以就没写。

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
template <typename T, typename ...Arg>
void r1(T &x, Arg & ... arg) {
    r1(x), r1(arg ...);
}

#define int long long
const int maxn = 1e3 + 5;
const int maxm = maxn << 1;
const int inf = 0x3f3f3f3f3f3f3f3f;
const int N = 2e2 + 5;
int n, T, S, E;
int tot(0);
struct Matrix {
    int a[N][N];
    Matrix(void) { memset(a, 0, sizeof(a)); }
    void Min() {
        memset(a, 0x3f, sizeof(a));
    }
    void init() {
        memset(a, 0x3f, sizeof(a));
        for(int i = 1; i <= tot; ++ i) a[i][i] = 0;
    }
    Matrix operator * (const Matrix &z) const {
        Matrix res; res.Min();
        for(int i = 1; i <= tot; ++ i) {
            for(int j = 1; j <= tot; ++ j) {
                for(int k = 1; k <= tot; ++ k) {
                    res.a[i][j] = min(res.a[i][j], a[i][k] + z.a[k][j]);
                }
            }
        }
        return res;
    }
}G, F;

int id[maxn];

Matrix ksm(Matrix x, int mi) {
    Matrix res; res.init();
    while(mi) {
        if(mi & 1) res = res * x;
        mi >>= 1;
        x = x * x;
    }
    return res;
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n, T, S, E);
    G.Min();
    id[S] = ++ tot, id[E] = ++ tot;
    for(i = 1; i <= T; ++ i) {
        int u, v, w;
        r1(w, u, v);
        auto get = [&] (const int &x) -> void{ if(!id[x]) id[x] = ++ tot; };
        auto Upd = [&] (const int &a, const int &b) -> void{ G.a[a][b] = min(G.a[a][b], w); };
        get(u), get(v);
        Upd(id[u], id[v]), Upd(id[v], id[u]);
    }
    Matrix res = ksm(G, n);
    printf("%lld\n", res.a[id[S]][id[E]]);
	return 0;
}
```



