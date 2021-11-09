---
title: CF1182E Product Oriented Recurrence
date: 2021-09-23 14:54:44
tags: 
  - 矩阵
  - 数论
categories: 
  - CF题解
---

<h3><center>CF1182E Product Oriented Recurrence</center></h3>

> [CF1182E Product Oriented Recurrence](https://www.luogu.com.cn/problem/CF1182E)

看到这个 $n$ 很大就会直接考虑矩阵乘法。

我们直接把指数拿下来即可，分成两部分进行计算。

对于 $c$ 的部分有这样的矩阵：
$$
\left[
\begin{matrix}
c_1 & c_2 & c_3 & 2n - 6 & 2
\end{matrix}
\right]
\times \left[
\begin{matrix}
0 & 0 & 1 & 0 & 0 \\
1 & 0 & 1 & 0 & 0\\
0 & 1 & 1 & 0 & 0 \\
0 & 0 & 1 & 1 & 0 \\
0 & 0 & 0 & 1 & 1
\end{matrix}
\right]
$$
然后我们考虑每个数肯定是可以表示成 $c^xf_1^yf_2^zf_3^{\lambda}$ 这样的形式，我们对于后面的系数也有递推式，我们直接分开计算即可。

$f$ 的递推矩阵：
$$
\left[
\begin{matrix}
c_1 & c_2 & c_3 
\end{matrix}
\right]
\times
\left[
\begin{matrix}
0 & 0 & 1 \\
1 & 0 & 1 \\
0 & 1 & 1
\end{matrix}
\right]
$$
别忘记了欧拉定理对于指数的取模是 $\varphi(mod)$。

```cpp
#include <bits/stdc++.h>
using namespace std;

//#define Fread
#define Getmod

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

#ifdef Getmod
const int mod  = 1e9 + 6;
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

//#define int long long
const int maxn = 2e5 + 5;
const int maxm = maxn << 1;

int ksm(int x,long long mi,int mod) {
    int res(1);
    while(mi) {
        if(mi & 1) res = 1ll * res * x % mod;
        mi >>= 1;
        x = 1ll * x * x % mod;
    }
    return res;
}

struct Matrix {
    Tm a[5][5];
    Matrix(void) {
        for(int i = 0; i < 5; ++ i) for(int j = 0; j < 5; ++ j)
            a[i][j] = 0;
    }
    void init() {
        for(int i = 0; i < 5; ++ i) a[i][i] = 1;
    }
    Matrix operator * (const Matrix &z) const {
        Matrix res;
        for(int i = 0; i < 5; ++ i) {
            for(int j = 0; j < 5; ++ j) {
                for(int k = 0; k < 5; ++ k) {
                    res.a[i][j] += a[i][k] * z.a[k][j];
                }
            }
        }
        return res;
    }
}F, tmpf, c, tmpc;

long long n;
int sc;

void ksm(Matrix &a, Matrix tmp,long long mi) {
    while(mi) {
        if(mi & 1) a = a * tmp;
        mi >>= 1;
        tmp = tmp * tmp;
    }
}

int Solve(int pos, int c) {
    for(int i = 0; i < 3; ++ i) if(i == pos) F.a[0][i] = 1; else F.a[0][i] = 0;
    ksm(F, tmpf, n - 3);
    return ksm(c, F.a[0][2].z, mod + 1);
}

int f[4];

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n, f[0], f[1], f[2], sc);
    tmpf.a[1][0] = 1;
    tmpf.a[2][1] = 1;
    tmpf.a[0][2] = tmpf.a[1][2] = tmpf.a[2][2] = 1;
    c.a[0][3] = c.a[0][4] = 2;

    tmpc.a[1][0] = 1;
    tmpc.a[2][1] = 1;
    tmpc.a[0][2] = tmpc.a[1][2] = tmpc.a[2][2] = tmpc.a[3][2] = 1;
    tmpc.a[3][3] = tmpc.a[4][3] = 1;
    tmpc.a[4][4] = 1;
    ksm(c, tmpc, n - 3);
//    printf("tn = %d\n", c.a[0][3]);

    int res(1);
    res = 1ll * res * ksm(sc, c.a[0][2].z, mod + 1);
    for(i = 0; i < 3; ++ i) res = 1ll * res * Solve(i, f[i]) % (mod + 1);
    printf("%d\n", res);
	return 0;
}

```




