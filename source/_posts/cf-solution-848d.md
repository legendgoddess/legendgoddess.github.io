---
title: CF848D Shake It!
date: 2021-08-08 10:51:44
tags:
  - 网络流
  - Dp
categories:
  - CF题解
---

### [CF848D](https://codeforces.com/problemset/problem/848/D)

对于一条 $S \to T$ 的路径我们一开始会考虑对于点的标号进行计算，但是事实上我们并不知道哪些点会在左边或者右边。

然后对于最小割我们转化成最大流进行计算，对于某一个中间点使得最大流是 $m$，你们左右两边的最大流的 $\min$ 就是 $m$。

我们发现本质上就是求经过 $n$ 次操作，使得最大流是 $m$ 的图的个数。

然后对于左右两边其实分成了一个子问题。

那么设 $f(n, m)$ 表示经过 $n$ 次操作，最大流是 $m$ 的图的个数。

然后进行转移的时候，设 $g1(n, m)$ 表示最大流大于等于 $m$ 的图的个数，$g(n, m)$ 是最大流恰好为 $m$ 的图的个数。

对于 $f$ 进行后缀和为 $F$。

那么 $g(n, m) = \sum_{i = 1} ^ {n - 1} F(i, m) \times F(n - i -1, m)$

之后因为每一条路径本质只会贡献一次，所以 $g(n, m) = g1(n, m) - g1(n, m + 1)$ 。

> 这个和二项式反演不同，那个是同一条路径贡献多次。

然后我们考虑计算 $f$。

然后因为每一个路径可以分开来计算，然后就是一个背包，将每一个路径都放起来。

对于不同的路径我们是可以直接相乘的，但是对于相同的路径会重复。如果有 $x$ 跳这样的路径，选 $y$ 条，那么要乘系数 $\binom{x + y - 1}{y}$。

```cpp
#include <bits/stdc++.h>
using namespace std;

//#define Fread
 #define Getmod

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

const int mod  = 1e9 + 7;
#ifdef Getmod
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

// #define int long long
const int maxn = 50 + 5;

Tm f[maxn][maxn], dp[maxn][maxn], g1[maxn][maxn], g[maxn][maxn], F[maxn][maxn];
Tm fac[maxn], inv[maxn];

int n, m;

#define Online
signed main() {
#ifndef Online
    freopen("S.in", "r", stdin);
    freopen("S.out", "w", stdout);
#endif
    auto ksm = [&] (Tm x,int mi) {
        Tm res(1);
        while(mi) {
            if(mi & 1) res *= x;
            mi >>= 1;
            x *= x;
        }
        return res;
    };
    auto init = [&] (int x = 50) {
        fac[0] = 1;
        for(int i = 1; i <= x; ++ i) fac[i] = fac[i - 1] * i;
        inv[x] = ksm(fac[x], mod - 2);
        for(int i = x - 1; ~ i; -- i) inv[i] = inv[i + 1] * (i + 1);
    };

    auto C = [&] (int a,int b) {
        if(a < b) return Tm(0);
        return fac[a] * inv[b] * inv[a - b];
    };

    auto calc = [&] (int n,int m,int a,int b) {
        Tm ans = dp[n][m];
        Tm cur(g[a][b]);
        for(int i = 1; i * a <= n && i * b <= m; ++ i) {
            ans += dp[n - i * a][m - i * b] * inv[i] * cur;
            cur *= g[a][b] + i;
        }
        return ans;
    };

    init();
    int i, j;
    r1(n, m);
    dp[0][0] = 1, f[0][1] = 1;
    for(i = 1; i <= n; ++ i) {
        for(j = n + 1; j; -- j)
            F[i - 1][j] = F[i - 1][j + 1] + f[i - 1][j];
        for(j = n + 1; ~ j; -- j) {
            g1[i][j] = 0;
            for(int k = 0; k < i; ++ k) g1[i][j] += F[k][j] * F[i - k - 1][j];
            g[i][j] = g1[i][j] - g1[i][j + 1];
        }
        for(j = 1; j <= n + 1; ++ j) {
            for(int x = n; ~ x; -- x)
                for(int y = n + 1; ~ y; -- y)
                dp[x][y] = calc(x, y, i, j);
        }
        for(j = 1; j <= n + 1; ++ j) f[i][j] = dp[i][j - 1];
    }
    printf("%d\n", f[n][m].z);
	return 0;
}

```




