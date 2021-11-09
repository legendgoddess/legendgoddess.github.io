---
title: CF1392I Kevin and Grid
date: 2021-08-25 15:04:33
tags:
  - Dp
categories:
  - CF题解
---

### [CF1392I Kevin and Grid](https://www.luogu.com.cn/problem/CF1392I)
> 做这题的时候感觉网上的讲解很不清楚。但是写完了之后发现其实我也讲不出什么东西 $\dots$

本质上这是一个联通块计数的问题。

我们考虑这个数据范围肯定不是给 $dp, dfs$ 的。

考虑是一个网格图，有一定的性质。考虑一下欧拉定理。

设联通块的个数是 $k$。

那么 $V - E + F  = k + 1$。

我们就可以通过这些求出联通块的个数。

但是不同的联通块会产生不同的贡献。我们在加上的联通块数量的贡献之后还需要加上包含在内部的贡献。

不妨设 $\ge x​$ 的点构成的联通块是 $A​$，另一个是 $B​$。

我们考虑网格图那么面（除了最外面的）肯定是四联通。或者是中间包含了一个 $A$ 的联通块。

那么本质上 $F_B - \text{B 中的 4 联通块} - 1$ 就是 $A$ 中包含的联通块。

设 $A$ 中的 $4$ 联通块是 $D_A$，$B$ 中的是 $D_B$。

那么最终的答案就是。
$$
\begin{aligned}
&V_A - E_A + F_A - V_B + E_B - F_B + In_A - In_B \\
=&V_A - E_A - V_B + E_B + D_A - D_B
\end{aligned}
$$
我们考虑如何统计这些信息。不妨拿 $A$ 的信息举例子。

- $4$ 联通块： 也就是 $4$ 个位置都 $\ge x$，求 $\min(a_i, a_{i - 1}) + \min(b_j, b_{j - 1}) \ge x$ 计算。
- 边 ： 对于横着的边求 $\min(b_i, b_{i - 1}) + a_j \ge x$ 即可，竖着就是 $\min(a_i, a_{i - 1}) + b_j \ge x$。
- 点： 直接 $FFT$ 即可。 

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
const int lim = (1 << 18), len = 18;
const int N = lim;
const double pi = acos(-1.0);
const int maxn = N + 20;

struct Complex {
    double x, y;
    Complex(double a = 0, double b = 0) : x(a), y(b) {}
    Complex operator + (const Complex &z) const { return Complex(x + z.x, y + z.y); }
    Complex operator - (const Complex &z) const { return Complex(x - z.x, y - z.y); }
    Complex operator * (const Complex &z) const { return Complex(x * z.x - y * z.y, x * z.y + y * z.x); }
    Complex & operator += (const Complex &z) { return *this = *this + z, *this; }
    Complex & operator -= (const Complex &z) { return *this = *this - z, *this; }
    Complex & operator *= (const Complex &z) { return *this = *this * z, *this; }
}tmp[maxn];

Complex mnA[maxn], mnB[maxn];

Complex mxA[maxn], mxB[maxn];

Complex nA[maxn], nB[maxn];

int rev[maxn];

void FFT(Complex *A,int opt) {
    for(int i = 0; i < lim; ++ i) if(i < rev[i]) swap(A[i], A[rev[i]]);
    for(int mid = 1; mid < lim; mid <<= 1) {
        Complex wn(cos(pi / mid), opt * sin(pi / mid));
        for(int j = 0, c = (mid << 1); j < lim; j += c) {
            Complex W(1, 0);
            for(int k = 0; k < mid; ++ k, W *= wn) {
                Complex x = A[j + k], y = W * A[j + k + mid];
                A[j + k] = x + y;
                A[j + k + mid] = x - y;
            }
        }
    }
    if(opt == -1) for(int i = 0; i < lim; ++ i) A[i].x /= lim;
}

int n, m, Q;
typedef long long ll;
int a[maxn], b[maxn];

void Mul(ll *ans,Complex *A, Complex *B) {
    for(int i = 0; i < lim; ++ i) tmp[i] = A[i] * B[i];
    FFT(tmp, -1);
    for(int i = 0; i < lim; ++ i) ans[i] += round(tmp[i].x);
}

ll V[maxn << 1], mxE[maxn << 1], mnE[maxn << 1], mxD[maxn << 1], mnD[maxn << 1];



signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    for(i = 0; i < lim; ++ i) rev[i] = (rev[i >> 1] >> 1) | ((i & 1) << (len - 1));
    r1(n, m, Q);
    for(i = 1; i <= n; ++ i) {
        r1(a[i]);
        nA[a[i]].x ++;
        if(i > 1)
            mnA[min(a[i - 1], a[i])].x ++,
            mxA[max(a[i - 1], a[i])].x ++;
    }
    for(i = 1; i <= m; ++ i) {
        r1(b[i]);
        nB[b[i]].x ++;
        if(i > 1)
            mnB[min(b[i - 1], b[i])].x ++,
            mxB[max(b[i - 1], b[i])].x ++;
    }
    FFT(nA, 1), FFT(nB, 1);
    FFT(mnA, 1), FFT(mxA, 1);
    FFT(mnB, 1), FFT(mxB, 1);

    Mul(V, nA, nB);
    for(i = 1; i <= N; ++ i) V[i] += V[i - 1];

    Mul(mxE, mnA, nB);
    Mul(mxE, nA, mnB);
    for(i = N; i >= 1; -- i) mxE[i] += mxE[i + 1];

    Mul(mnE, nA, mxB);
    Mul(mnE, mxA, nB);
    for(i = 1; i <= N; ++ i) mnE[i] += mnE[i - 1];

    Mul(mxD, mnA, mnB);
    for(i = N; i >= 1; -- i) mxD[i] += mxD[i + 1];

    Mul(mnD, mxA, mxB);
    for(i = 1; i <= N; ++ i) mnD[i] += mnD[i - 1];

    while(Q --) {
        int x; r1(x);
        long long ans(0);
        ans = 1ll * n * m - 2 * V[x - 1] + mnE[x - 1] - mxE[x] + mxD[x] - mnD[x - 1];
        printf("%lld\n", ans);
    }

	return 0;
}

```




