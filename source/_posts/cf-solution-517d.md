---
title: CF576D Flights for Regular Customers
date: 2021-09-24 16:29:30
tags: 
  - 矩阵
categories: 
  - CF题解
---

<h3><center>CF576D Flights for Regular Customers</center></h3>

> [CF576D Flights for Regular Customers](https://www.luogu.com.cn/problem/CF576D)

>  没想到用 $\tt bitset$。

首先考虑肯定是对于 $d$ 排序一下进行计算。

我们维护一个矩阵表示其中 $a_{u, v}$ 表示是否存在 $u \to v$ 的路径。

我们需要求出对于一个时间 $t$ 可以到达哪些点，之后我们就在只有这些边的图上进行广搜即可。

> 注意我们的邻接矩阵是存图的，之后我们更新能到达哪些点的时候是用传递闭包更新的。不要把两个弄错了。

可以保证每个答案都会被计算到。

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

const int maxn = 150 + 5;
const int maxm = maxn << 1;
typedef long long ll;
const ll inf = 1e18;
int n, m;
struct Edge {
    int u, v, d;
    int operator < (const Edge &z) const {
        return d < z.d;
    }
}E[maxn];

ll d[maxn];

struct Matrix {
    bitset<maxn> a[maxn];
    Matrix operator * (const Matrix &z) const {
        Matrix res;
        for(int i = 0; i < n; ++ i) {
            for(int k = 0; k < n; ++ k) if(a[i][k])
                res.a[i] |= z.a[k];
        }
        return res;
    }
}tp, F;

void ksm(Matrix& res, Matrix tmp, int mi) {
    while(mi) {
        if(mi & 1) res = res * tmp;
        mi >>= 1;
        tmp = tmp * tmp;
    }
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n, m);
    for(i = 1; i <= m; ++ i) {
        r1(E[i].u, E[i].v, E[i].d);
        -- E[i].u, -- E[i].v;
    }
    sort(E + 1, E + m + 1);
    F.a[0][0] = 1;
    int t(0);
    ll ans(inf);
    for(i = 1; i <= m; ++ i) {
        int las = t;
        t = E[i].d;
        ksm(F, tp, t - las);
        static queue<int> q; while(!q.empty()) q.pop();
        tp.a[E[i].u][E[i].v] = 1;
        for(j = 0; j < n; ++ j)
            if(F.a[0][j]) d[j] = 0, q.push(j);
            else d[j] = inf;
        while(!q.empty()) {
            int u = q.front(); q.pop();
            for(j = 0; j < n; ++ j) if(tp.a[u][j] && d[j] == inf) {
                d[j] = d[u] + 1;
                q.push(j);
            }
        }
        ans = min(ans, d[n - 1] + t);
    }
    if(ans == inf) return puts("Impossible"), 0;
    else printf("%lld\n", ans);
	return 0;
}
```




